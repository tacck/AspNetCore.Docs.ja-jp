---
title: 'ASP.NET Core での Entity Framework Core を使用した Razor Pages - チュートリアル 1/8'
author: rick-anderson
description: 'Entity Framework Core を使用して Razor Pages アプリを作成する方法について説明します'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365419"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="0e683-103">ASP.NET Core での Entity Framework Core を使用した Razor Pages - チュートリアル 1/8</span><span class="sxs-lookup"><span data-stu-id="0e683-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="0e683-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0e683-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0e683-105">これは、[ASP.NET Core Razor Pages](xref:razor-pages/index) アプリでの Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="0e683-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="0e683-106">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="0e683-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0e683-107">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0e683-108">このチュートリアルでは、コード ファーストのアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="0e683-109">データベース ファーストのアプローチを使用してこのチュートリアルを実行する方法の詳細については、[こちらの Github イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/16897)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0e683-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="0e683-110">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0e683-111">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0e683-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0e683-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0e683-112">Prerequisites</span></span>

* <span data-ttu-id="0e683-113">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0e683-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="0e683-116">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="0e683-116">Database engines</span></span>

<span data-ttu-id="0e683-117">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="0e683-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="0e683-118">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="0e683-119">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="0e683-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0e683-120">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0e683-120">Troubleshooting</span></span>

<span data-ttu-id="0e683-121">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="0e683-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0e683-122">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0e683-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="0e683-123">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="0e683-123">The sample app</span></span>

<span data-ttu-id="0e683-124">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="0e683-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="0e683-125">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="0e683-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0e683-126">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0e683-126">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="0e683-129">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="0e683-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="0e683-130">このチュートリアルでは、UI をカスタマイズする方法ではなく、EF Core と ASP.NET Core の使用方法について主に説明します。</span><span class="sxs-lookup"><span data-stu-id="0e683-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="0e683-131">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="0e683-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-133">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="0e683-134">**[ASP.NET Core Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="0e683-135">プロジェクトに *ContosoUniversity* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="0e683-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="0e683-136">コードをコピーして貼り付けるときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="0e683-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="0e683-137">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="0e683-137">Select **Create**.</span></span>
* <span data-ttu-id="0e683-138">ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 5.0]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e683-140">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="0e683-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="0e683-141">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-141">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0e683-142">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="0e683-142">Set up the site style</span></span>

<span data-ttu-id="0e683-143">次のコードをコピーして、 *Pages/Shared/_Layout.cshtml* ファイルに貼り付けます: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="0e683-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="0e683-144">このレイアウト ファイルによってサイト ヘッダー、フッター、およびメニューが設定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="0e683-145">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="0e683-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="0e683-146">"ContosoUniversity" をいずれも "Contoso University" へ変更。</span><span class="sxs-lookup"><span data-stu-id="0e683-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0e683-147">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="0e683-147">There are three occurrences.</span></span>
* <span data-ttu-id="0e683-148">**[ホーム]** および **[プライバシー]** メニュー エントリが削除されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="0e683-149">**[バージョン情報]** 、 **[学生]** 、 **[コース]** 、 **[講師]** 、および **[部門]** のエントリが追加されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="0e683-150">*Pages/Index.cshtml* で、ファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0e683-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="0e683-151">上記のコードでは、ASP.NET Core に関するテキストがこのアプリに関するテキストに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="0e683-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="0e683-152">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="0e683-153">データ モデル</span><span class="sxs-lookup"><span data-stu-id="0e683-153">The data model</span></span>

<span data-ttu-id="0e683-154">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-154">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="0e683-156">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="0e683-157">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-157">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="0e683-159">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="0e683-160">次のコードを使用して、 *Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="0e683-161">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0e683-162">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="0e683-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0e683-163">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="0e683-164">詳細については、[EF Core - キー](/ef/core/modeling/keys?tabs=data-annotations)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="0e683-165">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="0e683-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0e683-166">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0e683-167">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="0e683-168">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="0e683-169">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="0e683-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="0e683-170">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="0e683-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="0e683-171">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="0e683-172">StudentID は、Enrollment テーブルの *外部キー* です。</span><span class="sxs-lookup"><span data-stu-id="0e683-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="0e683-173">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="0e683-174">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="0e683-175">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="0e683-176">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-176">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="0e683-178">以下のコードを使用して、 *Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="0e683-179">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0e683-180">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="0e683-181">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="0e683-182">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="0e683-183">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0e683-184">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="0e683-185">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="0e683-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0e683-186">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0e683-187">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="0e683-188">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0e683-189">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="0e683-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0e683-190">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="0e683-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0e683-191">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="0e683-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0e683-192">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="0e683-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0e683-193">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="0e683-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0e683-194">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-194">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="0e683-196">以下のコードを使用して、 *Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="0e683-197">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="0e683-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0e683-198">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="0e683-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0e683-199">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="0e683-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="0e683-200">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="0e683-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="0e683-201">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="0e683-201">Scaffold Student pages</span></span>

<span data-ttu-id="0e683-202">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="0e683-203">EF Core `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="0e683-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="0e683-204">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="0e683-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="0e683-205">これは <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="0e683-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="0e683-206">`Student` エンティティの作成、読み取り、更新、および削除 (CRUD) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="0e683-206">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-208">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="0e683-209">**ソリューション エクスプローラー** で、 *Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0e683-210">**[新しいスキャフォールディング アイテムの追加]** ダイアログで次のようにします。</span><span class="sxs-lookup"><span data-stu-id="0e683-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="0e683-211">左側のタブで、 **[インストール済み] > [共通] > [Razor Pages]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-211">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="0e683-212">**[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-212">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="0e683-213">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="0e683-213">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="0e683-214">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="0e683-215">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="0e683-216">データ コンテキスト名が、`ContosoUniversityContext` ではなく `SchoolContext` で終わるように変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="0e683-217">更新されたコンテキスト名: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="0e683-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="0e683-218">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="0e683-218">Select **Add**.</span></span>

<span data-ttu-id="0e683-219">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="0e683-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e683-221">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0e683-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="0e683-222">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e683-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="0e683-223">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e683-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="0e683-224">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="0e683-225">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="0e683-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="0e683-226">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="0e683-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="0e683-227">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="0e683-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="0e683-228">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="0e683-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="0e683-229">前の手順が失敗した場合は、プロジェクトをビルドし、スキャフォールディング手順を再試行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="0e683-230">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0e683-230">The scaffolding process:</span></span>

* <span data-ttu-id="0e683-231">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-231">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="0e683-232">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="0e683-233">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="0e683-234">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="0e683-235">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="0e683-236">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="0e683-237">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="0e683-238">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="0e683-239">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-239">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="0e683-240">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="0e683-240">Database connection string</span></span>

<span data-ttu-id="0e683-241">スキャフォールディング ツールを使用すると、 *appsettings.json* ファイルに接続文字列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-241">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e683-243">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="0e683-244">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="0e683-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0e683-245">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e683-247">SQLite 接続文字列を *CU.db* に短縮します。</span><span class="sxs-lookup"><span data-stu-id="0e683-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="0e683-248">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="0e683-248">Update the database context class</span></span>

<span data-ttu-id="0e683-249">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="0e683-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0e683-250">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="0e683-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0e683-251">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0e683-252">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="0e683-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0e683-253">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="0e683-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="0e683-254">上記のコードは、単数形の `DbSet<Student> Student` から複数形の `DbSet<Student> Students` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="0e683-255">Razor Pages のコードが新しい `DBSet` 名と一致するようにするには、`_context.Student.` から</span><span class="sxs-lookup"><span data-stu-id="0e683-255">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="0e683-256">`_context.Students.` へとグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-256">to: `_context.Students.`</span></span>

<span data-ttu-id="0e683-257">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-257">There are 8 occurrences.</span></span>

<span data-ttu-id="0e683-258">エンティティ セットには複数のエンティティが含まれているため、開発者の多くは `DBSet` プロパティ名を複数形にすることを好みます。</span><span class="sxs-lookup"><span data-stu-id="0e683-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="0e683-259">強調表示されたコード:</span><span class="sxs-lookup"><span data-stu-id="0e683-259">The highlighted code:</span></span>

* <span data-ttu-id="0e683-260">エンティティ セットごとに [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0e683-261">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="0e683-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="0e683-262">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="0e683-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="0e683-263">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="0e683-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="0e683-264"><xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="0e683-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="0e683-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="0e683-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="0e683-266">`SchoolContext` が初期化されたとき、ただしモデルがロックダウンされてコンテキストの初期化に使用される前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="0e683-267">チュートリアルの後半で `Student` エンティティが他のエンティティへの参照を取得することから、必須となります。</span><span class="sxs-lookup"><span data-stu-id="0e683-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="0e683-268">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="0e683-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0e683-269">Startup.cs</span></span>

<span data-ttu-id="0e683-270">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="0e683-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0e683-271">サービス (`SchoolContext` など) は、アプリの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="0e683-272">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-272">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="0e683-273">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0e683-274">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="0e683-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e683-276">次の強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="0e683-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e683-278">スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="0e683-279">運用データベースの使用の詳細については、「[開発用に SQLite を、運用環境に SQL Server を使用する](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="0e683-280">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0e683-281">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="0e683-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="0e683-282">データベース例外フィルターを追加する</span><span class="sxs-lookup"><span data-stu-id="0e683-282">Add the database exception filter</span></span>

<span data-ttu-id="0e683-283">次のコードに示すように、`ConfigureServices` に <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> を追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="0e683-285">[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="0e683-286">PMC で、次のように入力して NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="0e683-288">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet パッケージには Entity Framework Core のエラー ページ用の ASP.NET Core ミドルウェアが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0e683-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="0e683-289">このミドルウェアは、Entity Framework Core の移行に関するエラーを検出して診断するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="0e683-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="0e683-290">`AddDatabaseDeveloperPageExceptionFilter` により、[開発環境](xref:fundamentals/environments)で役に立つエラー情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0e683-291">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="0e683-291">Create the database</span></span>

<span data-ttu-id="0e683-292">データベースが存在しない場合は、 *Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0e683-293">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="0e683-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="0e683-294">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="0e683-295">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="0e683-296">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="0e683-296">Delete the database.</span></span> <span data-ttu-id="0e683-297">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="0e683-297">Any existing data is lost.</span></span>
* <span data-ttu-id="0e683-298">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-298">Change the data model.</span></span> <span data-ttu-id="0e683-299">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="0e683-300">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-300">Run the app.</span></span>
* <span data-ttu-id="0e683-301">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="0e683-302">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="0e683-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="0e683-303">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="0e683-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="0e683-304">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="0e683-305">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="0e683-306">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="0e683-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0e683-307">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="0e683-307">Test the app</span></span>

* <span data-ttu-id="0e683-308">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-308">Run the app.</span></span>
* <span data-ttu-id="0e683-309">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0e683-310">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="0e683-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0e683-311">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="0e683-311">Seed the database</span></span>

<span data-ttu-id="0e683-312">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="0e683-313">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="0e683-314">次のコードを使用して、 *Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="0e683-315">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="0e683-316">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="0e683-317">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0e683-318">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0e683-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e683-320">アプリが実行されている場合は停止し、 **パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="0e683-321">`Y` で応答すると、データベースが削除されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e683-323">アプリが実行されている場合は停止し、 *CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="0e683-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="0e683-324">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="0e683-324">Restart the app.</span></span>
* <span data-ttu-id="0e683-325">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="0e683-326">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="0e683-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-328">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="0e683-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0e683-329">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="0e683-330">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="0e683-331">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="0e683-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="0e683-332">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="0e683-333">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e683-335">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="0e683-336">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="0e683-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="0e683-337">非同期コード</span><span class="sxs-lookup"><span data-stu-id="0e683-337">Asynchronous code</span></span>

<span data-ttu-id="0e683-338">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="0e683-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0e683-339">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0e683-340">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="0e683-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0e683-341">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="0e683-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0e683-342">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0e683-343">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="0e683-344">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="0e683-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0e683-345">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0e683-346">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="0e683-347">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0e683-348">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="0e683-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0e683-349">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="0e683-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="0e683-350">戻り値の型 `Task<T>` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="0e683-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="0e683-351">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="0e683-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0e683-352">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="0e683-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0e683-353">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="0e683-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0e683-354">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="0e683-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0e683-355">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="0e683-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0e683-356">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0e683-357">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0e683-358">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="0e683-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0e683-359">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="0e683-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0e683-360">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="0e683-361">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="0e683-362">パフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="0e683-362">Performance considerations</span></span>

<span data-ttu-id="0e683-363">一般に、Web ページで任意の数の行を読み込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="0e683-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="0e683-364">クエリでは、ページングまたは制限アプローチを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="0e683-365">たとえば、上記のクエリでは `Take` を使用して、返される行を制限できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0e683-366">ビューで大きいテーブルを列挙すると、列挙の途中でデータベース例外が発生した場合、部分的に構築された HTTP 200 応答が返される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="0e683-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> の既定値は 1024 です。</span><span class="sxs-lookup"><span data-stu-id="0e683-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="0e683-368">次のコードでは、`MaxModelBindingCollectionSize` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="0e683-369">ページングについては、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="0e683-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0e683-370">次の手順</span><span class="sxs-lookup"><span data-stu-id="0e683-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0e683-371">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="0e683-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0e683-372">これは、[ASP.NET Core Razor Pages](xref:razor-pages/index) アプリでの Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="0e683-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="0e683-373">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="0e683-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0e683-374">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0e683-375">このチュートリアルでは、コード ファーストのアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="0e683-376">データベース ファーストのアプローチを使用してこのチュートリアルを実行する方法の詳細については、[こちらの Github イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/16897)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0e683-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="0e683-377">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0e683-378">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0e683-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0e683-379">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0e683-379">Prerequisites</span></span>

* <span data-ttu-id="0e683-380">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0e683-380">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="0e683-383">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="0e683-383">Database engines</span></span>

<span data-ttu-id="0e683-384">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="0e683-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="0e683-385">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="0e683-386">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="0e683-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0e683-387">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0e683-387">Troubleshooting</span></span>

<span data-ttu-id="0e683-388">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="0e683-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0e683-389">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0e683-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="0e683-390">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="0e683-390">The sample app</span></span>

<span data-ttu-id="0e683-391">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="0e683-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="0e683-392">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="0e683-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0e683-393">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0e683-393">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="0e683-396">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="0e683-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="0e683-397">このチュートリアルでは、UI をカスタマイズする方法ではなく、主に EF Core の使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0e683-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="0e683-398">ページの上部にあるリンクを使用して、完成したプロジェクトのソース コードを取得します。</span><span class="sxs-lookup"><span data-stu-id="0e683-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="0e683-399">*cu30* フォルダーには、チュートリアルの ASP.NET Core 3.0 バージョン用のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e683-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="0e683-400">チュートリアル 1-7 のコードの状態を反映するファイルは、 *cu30snapshots* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="0e683-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e683-402">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="0e683-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0e683-403">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="0e683-403">Build the project.</span></span>
* <span data-ttu-id="0e683-404">パッケージ マネージャー コンソール (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="0e683-405">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="0e683-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e683-407">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="0e683-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="0e683-408">*ContosoUniversity.csproj* を削除し、 *ContosoUniversitySQLite.csproj* の名前を *ContosoUniversity.csproj* に変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="0e683-409">*Program.cs* で、`StartupSQLite` が使用されるように `#define Startup` をコメント アウトします。</span><span class="sxs-lookup"><span data-stu-id="0e683-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="0e683-410">*appSettings.json* を削除し、 *appSettingsSQLite.json* の名前を *appSettings.json* に変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="0e683-411">*Migrations* フォルダーを削除し、 *MigrationsSQL* の名前を *Migrations* に変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="0e683-412">`#if SQLiteVersion` のグローバル検索を実行し、`#if SQLiteVersion` と関連する `#endif` ステートメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="0e683-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="0e683-413">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="0e683-413">Build the project.</span></span>
* <span data-ttu-id="0e683-414">プロジェクト フォルダーのコマンド プロンプトで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="0e683-415">SQLite ツールで、次の SQL ステートメントを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="0e683-416">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="0e683-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="0e683-417">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="0e683-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-419">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0e683-420">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="0e683-421">プロジェクトに *ContosoUniversity* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="0e683-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="0e683-422">コードをコピーして貼り付けるときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="0e683-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="0e683-423">ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e683-425">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="0e683-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="0e683-426">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-426">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0e683-427">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="0e683-427">Set up the site style</span></span>

<span data-ttu-id="0e683-428">*Pages/Shared/_Layout.cshtml* を更新して、サイト ヘッダー、フッター、およびメニューを設定します。</span><span class="sxs-lookup"><span data-stu-id="0e683-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="0e683-429">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0e683-430">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="0e683-430">There are three occurrences.</span></span>

* <span data-ttu-id="0e683-431">**Home** メニューと **Privacy** メニューのエントリを削除し、 **About** 、 **Students** 、 **Courses** 、 **Instructors** 、 **Departments** のエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="0e683-432">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="0e683-433">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET Core に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="0e683-434">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="0e683-435">データ モデル</span><span class="sxs-lookup"><span data-stu-id="0e683-435">The data model</span></span>

<span data-ttu-id="0e683-436">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-436">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="0e683-438">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="0e683-439">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-439">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="0e683-441">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="0e683-442">次のコードを使用して、 *Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="0e683-443">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="0e683-444">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="0e683-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0e683-445">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="0e683-446">詳細については、[EF Core - キー](/ef/core/modeling/keys?tabs=data-annotations)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="0e683-447">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="0e683-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0e683-448">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="0e683-449">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="0e683-450">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="0e683-451">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="0e683-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="0e683-452">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="0e683-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="0e683-453">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="0e683-454">StudentID は、Enrollment テーブルの *外部キー* です。</span><span class="sxs-lookup"><span data-stu-id="0e683-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="0e683-455">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="0e683-456">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="0e683-457">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="0e683-458">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-458">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="0e683-460">以下のコードを使用して、 *Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="0e683-461">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="0e683-462">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="0e683-463">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="0e683-464">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="0e683-465">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0e683-466">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="0e683-467">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="0e683-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0e683-468">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0e683-469">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="0e683-470">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0e683-471">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="0e683-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0e683-472">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="0e683-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0e683-473">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="0e683-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0e683-474">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="0e683-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0e683-475">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="0e683-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="0e683-476">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-476">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="0e683-478">以下のコードを使用して、 *Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="0e683-479">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="0e683-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0e683-480">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="0e683-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0e683-481">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="0e683-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="0e683-482">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="0e683-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="0e683-483">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="0e683-483">Scaffold Student pages</span></span>

<span data-ttu-id="0e683-484">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="0e683-485">EF Core *コンテキスト* クラス。</span><span class="sxs-lookup"><span data-stu-id="0e683-485">An EF Core *context* class.</span></span> <span data-ttu-id="0e683-486">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="0e683-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="0e683-487">これは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="0e683-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="0e683-488">`Student` エンティティの作成、読み取り、更新、および削除 (CRUD) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="0e683-488">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-490">*Pages* フォルダー内に *Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="0e683-491">**ソリューション エクスプローラー** で、 *Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0e683-492">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-492">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="0e683-493">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="0e683-493">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="0e683-494">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="0e683-495">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="0e683-496">データ コンテキスト名を *ContosoUniversity.Models.ContosoUniversityContext* から *ContosoUniversity.Data.SchoolContext* に変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="0e683-497">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="0e683-497">Select **Add**.</span></span>

<span data-ttu-id="0e683-498">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="0e683-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e683-500">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0e683-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="0e683-501">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e683-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="0e683-502">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="0e683-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="0e683-503">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="0e683-504">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="0e683-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="0e683-505">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="0e683-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="0e683-506">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="0e683-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="0e683-507">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="0e683-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="0e683-508">前の手順で問題が発生した場合は、プロジェクトをビルドし、スキャフォールディング ステップを再試行してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="0e683-509">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0e683-509">The scaffolding process:</span></span>

* <span data-ttu-id="0e683-510">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-510">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="0e683-511">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="0e683-512">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="0e683-513">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="0e683-514">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="0e683-515">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="0e683-516">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="0e683-517">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="0e683-518">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-518">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="0e683-519">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="0e683-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e683-521">*appsettings.json* ファイルでは、接続文字列 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されています。</span><span class="sxs-lookup"><span data-stu-id="0e683-521">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="0e683-522">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="0e683-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0e683-523">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e683-525">*CU.db* という名前の SQLite データベース ファイルを指すように接続文字列を変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="0e683-526">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="0e683-526">Update the database context class</span></span>

<span data-ttu-id="0e683-527">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="0e683-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="0e683-528">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="0e683-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0e683-529">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0e683-530">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="0e683-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0e683-531">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="0e683-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="0e683-532">強調表示されたコードによって、各エンティティ セットの [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0e683-533">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="0e683-533">In EF Core terminology:</span></span>

* <span data-ttu-id="0e683-534">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="0e683-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="0e683-535">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="0e683-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0e683-536">エンティティ セットには複数のエンティティが含まれているため、DBSet プロパティは複数形の名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="0e683-537">スキャフォールディング ツールによって `Student` DBSet を作成したので、このステップでこれを複数形の `Students` に変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="0e683-538">Razor Pages のコードを新しい DBSet 名と一致させるには、プロジェクト全体で `_context.Student` を `_context.Students` にグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-538">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="0e683-539">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-539">There are 8 occurrences.</span></span>

<span data-ttu-id="0e683-540">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="0e683-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0e683-541">Startup.cs</span></span>

<span data-ttu-id="0e683-542">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="0e683-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0e683-543">サービス (EF Core データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0e683-544">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-544">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0e683-545">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0e683-546">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="0e683-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-548">`ConfigureServices` では、強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="0e683-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e683-550">`ConfigureServices` では、スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="0e683-551">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0e683-552">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="0e683-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="0e683-553">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="0e683-553">Create the database</span></span>

<span data-ttu-id="0e683-554">データベースが存在しない場合は、 *Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="0e683-555">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="0e683-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="0e683-556">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="0e683-557">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="0e683-558">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="0e683-558">Delete the database.</span></span> <span data-ttu-id="0e683-559">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="0e683-559">Any existing data is lost.</span></span>
* <span data-ttu-id="0e683-560">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-560">Change the data model.</span></span> <span data-ttu-id="0e683-561">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="0e683-562">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-562">Run the app.</span></span>
* <span data-ttu-id="0e683-563">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="0e683-564">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="0e683-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="0e683-565">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="0e683-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="0e683-566">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="0e683-567">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="0e683-568">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="0e683-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0e683-569">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="0e683-569">Test the app</span></span>

* <span data-ttu-id="0e683-570">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-570">Run the app.</span></span>
* <span data-ttu-id="0e683-571">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0e683-572">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="0e683-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="0e683-573">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="0e683-573">Seed the database</span></span>

<span data-ttu-id="0e683-574">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="0e683-575">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="0e683-576">次のコードを使用して、 *Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="0e683-577">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="0e683-578">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="0e683-579">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="0e683-580">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0e683-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e683-582">アプリが実行されている場合は停止し、 **パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e683-584">アプリが実行されている場合は停止し、 *CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="0e683-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="0e683-585">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="0e683-585">Restart the app.</span></span>

* <span data-ttu-id="0e683-586">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="0e683-587">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="0e683-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-589">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="0e683-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="0e683-590">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="0e683-591">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="0e683-592">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="0e683-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="0e683-593">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="0e683-594">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e683-596">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="0e683-597">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="0e683-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="0e683-598">非同期コード</span><span class="sxs-lookup"><span data-stu-id="0e683-598">Asynchronous code</span></span>

<span data-ttu-id="0e683-599">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="0e683-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0e683-600">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0e683-601">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="0e683-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0e683-602">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="0e683-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0e683-603">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0e683-604">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="0e683-605">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="0e683-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0e683-606">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0e683-607">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="0e683-608">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0e683-609">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="0e683-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0e683-610">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="0e683-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="0e683-611">戻り値の型 `Task<T>` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="0e683-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="0e683-612">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="0e683-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0e683-613">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="0e683-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0e683-614">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="0e683-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0e683-615">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="0e683-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0e683-616">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="0e683-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0e683-617">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="0e683-618">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0e683-619">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="0e683-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0e683-620">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="0e683-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0e683-621">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="0e683-622">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="0e683-623">次の手順</span><span class="sxs-lookup"><span data-stu-id="0e683-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0e683-624">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="0e683-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0e683-625">Contoso University のサンプル Web アプリでは、Entity Framework (EF) Core を使用して ASP.NET Core Razor Pages アプリを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0e683-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="0e683-626">このサンプル アプリは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="0e683-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="0e683-627">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0e683-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="0e683-628">このページは、Contoso University のサンプル アプリを作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="0e683-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="0e683-629">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="0e683-630">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0e683-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0e683-631">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0e683-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="0e683-634">[Razor Pages](xref:razor-pages/index) に関する知識。</span><span class="sxs-lookup"><span data-stu-id="0e683-634">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="0e683-635">そのプログラミング経験をお持ちでない場合は、このシリーズを始める前に [Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するチュートリアルを完了してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-635">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0e683-636">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0e683-636">Troubleshooting</span></span>

<span data-ttu-id="0e683-637">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="0e683-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="0e683-638">ヘルプが必要なときは、[StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) について質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0e683-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="0e683-639">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="0e683-639">The Contoso University web app</span></span>

<span data-ttu-id="0e683-640">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="0e683-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="0e683-641">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="0e683-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="0e683-642">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0e683-642">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

<span data-ttu-id="0e683-645">このサイトの UI スタイルは、組み込みのテンプレートによって生成されるものに類似しています。</span><span class="sxs-lookup"><span data-stu-id="0e683-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="0e683-646">このチュートリアルでは、UI ではなく、EF Core と Razor ページに重点を置きます。</span><span class="sxs-lookup"><span data-stu-id="0e683-646">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="0e683-647">ContosoUniversity Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="0e683-647">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-649">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="0e683-650">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="0e683-651">プロジェクトに **ContosoUniversity** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="0e683-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="0e683-652">このプロジェクトに *ContosoUniversity* という名前を付けることは重要です。そうすることでコードをコピーしたり貼り付けるときに名前空間が一致します。</span><span class="sxs-lookup"><span data-stu-id="0e683-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="0e683-653">ドロップダウン リストで **[ASP.NET Core 2.1]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="0e683-654">前の手順の画像については、[Razor Web アプリの作成](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="0e683-654">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="0e683-655">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="0e683-657">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="0e683-657">Set up the site style</span></span>

<span data-ttu-id="0e683-658">変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="0e683-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="0e683-659">*Pages/Shared/_Layout.cshtml* に次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="0e683-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="0e683-660">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="0e683-661">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="0e683-661">There are three occurrences.</span></span>

* <span data-ttu-id="0e683-662">メニュー エントリとして「 **Students** 」、「 **Courses** 」、「 **Instructors** 」、「 **Departments** 」を追加し、「 **Contact** 」を削除します。</span><span class="sxs-lookup"><span data-stu-id="0e683-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="0e683-663">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-663">The changes are highlighted.</span></span> <span data-ttu-id="0e683-664">(マークアップは一部表示されて *いません* 。)</span><span class="sxs-lookup"><span data-stu-id="0e683-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="0e683-665">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="0e683-666">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="0e683-666">Create the data model</span></span>

<span data-ttu-id="0e683-667">Contoso University アプリのエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="0e683-668">次の 3 つのエンティティから始めます。</span><span class="sxs-lookup"><span data-stu-id="0e683-668">Start with the following three entities:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="0e683-670">`Student` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="0e683-671">`Course` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="0e683-672">1 人の学生をさまざまな講座に登録できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="0e683-673">1 つの講座に任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="0e683-674">次のセクションでは、エンティティごとにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="0e683-675">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-675">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="0e683-677">*Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-677">Create a *Models* folder.</span></span> <span data-ttu-id="0e683-678">以下のコードを使用して、 *Models* フォルダーで、 *Student.cs* という名前のクラス ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="0e683-679">`ID` プロパティは、このクラスに相当するデータベース (DB) テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="0e683-680">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="0e683-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="0e683-681">`classnameID` の `classname` はクラスの名前です。</span><span class="sxs-lookup"><span data-stu-id="0e683-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="0e683-682">代わりの自動的に認識される主キーは、前の例の `StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="0e683-683">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="0e683-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="0e683-684">ナビゲーション プロパティは、このエンティティに関連する他のエンティティにリンクします。</span><span class="sxs-lookup"><span data-stu-id="0e683-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="0e683-685">この例では、`Student entity` の `Enrollments` プロパティによって、その `Student` に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="0e683-686">たとえば、DB 内のある Student 行に 2 つ関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="0e683-687">関連する `Enrollment` 行とは、その学生の主キー値を `StudentID` 列に含む行です。</span><span class="sxs-lookup"><span data-stu-id="0e683-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="0e683-688">たとえば、ID=1 の学生には、`Enrollment` テーブルに行が 2 つあるとします。</span><span class="sxs-lookup"><span data-stu-id="0e683-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="0e683-689">その `Enrollment` テーブルには、`StudentID` = 1 の行が 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="0e683-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="0e683-690">`StudentID` は `Enrollment` テーブルの外部キーであり、`Student` テーブルでその学生を指定します。</span><span class="sxs-lookup"><span data-stu-id="0e683-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="0e683-691">あるナビゲーション プロパティが複数のエンティティを保持できる場合、そのナビゲーション プロパティは `ICollection<T>` のようなリスト型にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="0e683-692">`ICollection<T>` を指定するか、`List<T>` や `HashSet<T>` のような型を指定できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="0e683-693">`ICollection<T>` を使用する場合、EF Core で `HashSet<T>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="0e683-694">複数のエンティティを保持するナビゲーション プロパティは、多対多または一対多の関係から発生します。</span><span class="sxs-lookup"><span data-stu-id="0e683-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="0e683-695">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-695">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="0e683-697">以下のコードを使用して、 *Models* フォルダーで、 *Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="0e683-698">`EnrollmentID` プロパティは主キーです。</span><span class="sxs-lookup"><span data-stu-id="0e683-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="0e683-699">このエンティティでは、`Student` エンティティのような `ID` ではなく、`classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="0e683-700">一般的に、開発者は 1 つのパターンを選択し、データ モデル全体でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e683-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="0e683-701">後のチュートリアルでは、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装します。</span><span class="sxs-lookup"><span data-stu-id="0e683-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="0e683-702">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="0e683-703">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="0e683-704">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="0e683-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="0e683-705">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="0e683-706">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="0e683-707">`Student` エンティティは、複数の `Enrollment` エンティティが含まれる `Student.Enrollments` ナビゲーション プロパティとは異なります。</span><span class="sxs-lookup"><span data-stu-id="0e683-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="0e683-708">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="0e683-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="0e683-709">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="0e683-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="0e683-710">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="0e683-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="0e683-711">たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーは `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="0e683-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="0e683-712">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="0e683-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="0e683-713">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="0e683-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="0e683-714">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="0e683-714">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="0e683-716">以下のコードを使用して、 *Models* フォルダーで、 *Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="0e683-717">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="0e683-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="0e683-718">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="0e683-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="0e683-719">`DatabaseGenerated` 属性によって、アプリで主キーを指定できます。DB に生成させるのではありません。</span><span class="sxs-lookup"><span data-stu-id="0e683-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="0e683-720">Student モデルをスキャホールディングする</span><span class="sxs-lookup"><span data-stu-id="0e683-720">Scaffold the student model</span></span>

<span data-ttu-id="0e683-721">このセクションでは、Student モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="0e683-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="0e683-722">つまり、スキャフォールディング ツールにより、Student モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="0e683-723">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="0e683-723">Build the project.</span></span>
* <span data-ttu-id="0e683-724">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0e683-726">**ソリューション エクスプローラー** で、 *Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="0e683-727">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-727">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="0e683-728">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="0e683-728">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="0e683-729">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="0e683-730">**[データ コンテキスト クラス]** 行で **+** (+) 記号を選択し、生成された名前を **ContosoUniversity.Models.SchoolContext** に変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="0e683-731">**[データ コンテキスト クラス]** ドロップダウンで、 **[ContosoUniversity.Models.SchoolContext]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="0e683-732">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="0e683-732">Select **Add**.</span></span>

![CRUD ダイアログ](intro/_static/s1.png)

<span data-ttu-id="0e683-734">前の手順に問題がある場合は、「[ムービー モデルのスキャフォールディング](xref:tutorials/razor-pages/model#scaffold-the-movie-model)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0e683-736">次のコマンドを実行して、Student モデルをスキャホールディングします。</span><span class="sxs-lookup"><span data-stu-id="0e683-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="0e683-737">スキャフォールディングのプロセスが作成され、次のファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="0e683-738">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="0e683-738">Files created</span></span>

* <span data-ttu-id="0e683-739">*Pages/Students* 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="0e683-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="0e683-740">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="0e683-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="0e683-741">ファイルの更新</span><span class="sxs-lookup"><span data-stu-id="0e683-741">File updates</span></span>

* <span data-ttu-id="0e683-742">*Startup.cs* :このファイルに対する変更を次のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="0e683-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="0e683-743">*appsettings.json* :ローカル データベースへの接続に使用される接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-743">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="0e683-744">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="0e683-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="0e683-745">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="0e683-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0e683-746">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="0e683-747">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-747">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="0e683-748">db コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="0e683-749">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="0e683-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="0e683-750">*Startup.cs* の `ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="0e683-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="0e683-751">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="0e683-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="0e683-752">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="0e683-753">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="0e683-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="0e683-754">main を更新する</span><span class="sxs-lookup"><span data-stu-id="0e683-754">Update main</span></span>

<span data-ttu-id="0e683-755">*Program.cs* で、次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="0e683-756">依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="0e683-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="0e683-757">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0e683-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="0e683-758">`EnsureCreated` メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="0e683-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="0e683-759">次は、更新された *Program.cs* ファイルのコードです。</span><span class="sxs-lookup"><span data-stu-id="0e683-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="0e683-760">`EnsureCreated` で、コンテキストのデータベースが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="0e683-761">存在する場合、アクションは何も実行されません。</span><span class="sxs-lookup"><span data-stu-id="0e683-761">If it exists, no action is taken.</span></span> <span data-ttu-id="0e683-762">存在しない場合は、データベースとそのすべてのスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="0e683-763">`EnsureCreated` は、データベースの作成に移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="0e683-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="0e683-764">`EnsureCreated` で作成されたデータベースは、後で移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="0e683-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="0e683-765">アプリの起動時に `EnsureCreated` が呼び出され、次のワークフローが可能になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="0e683-766">DB を削除します。</span><span class="sxs-lookup"><span data-stu-id="0e683-766">Delete the DB.</span></span>
* <span data-ttu-id="0e683-767">DB スキーマを変更します (たとえば、`EmailAddress` フィールドを追加します)。</span><span class="sxs-lookup"><span data-stu-id="0e683-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="0e683-768">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-768">Run the app.</span></span>
* <span data-ttu-id="0e683-769">`EnsureCreated` によって、`EmailAddress` 列を含む DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="0e683-770">`EnsureCreated` は、スキーマが急速に進化している開発の早期に便利です。</span><span class="sxs-lookup"><span data-stu-id="0e683-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="0e683-771">このチュートリアルの後半では、DB は削除され、移行が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="0e683-772">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="0e683-772">Test the app</span></span>

<span data-ttu-id="0e683-773">アプリを実行し、cookie ポリシーに同意します。</span><span class="sxs-lookup"><span data-stu-id="0e683-773">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="0e683-774">このアプリで個人情報が保持されることはありません。</span><span class="sxs-lookup"><span data-stu-id="0e683-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="0e683-775">cookie ポリシーについては、[EU の一般的なデータ保護規制 (GDPR) のサポート](xref:security/gdpr)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-775">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="0e683-776">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0e683-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="0e683-777">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="0e683-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="0e683-778">SchoolContext DB コンテキストを確認する</span><span class="sxs-lookup"><span data-stu-id="0e683-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="0e683-779">所与のデータ モデルの EF Core 機能を調整するメイン クラスは、DB コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="0e683-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="0e683-780">データ コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="0e683-781">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="0e683-782">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="0e683-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="0e683-783">次のコードを使用して *SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="0e683-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="0e683-784">強調表示されたコードによって、各エンティティ セットの [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="0e683-785">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="0e683-785">In EF Core terminology:</span></span>

* <span data-ttu-id="0e683-786">エンティティ セットは通常、DB テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="0e683-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="0e683-787">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="0e683-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="0e683-788">`DbSet<Enrollment>` と `DbSet<Course>` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="0e683-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="0e683-789">EF Core にはそれらが暗黙的に含まれます。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。</span><span class="sxs-lookup"><span data-stu-id="0e683-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="0e683-790">このチュートリアルでは、`SchoolContext` に `DbSet<Enrollment>` と `DbSet<Course>` を残します。</span><span class="sxs-lookup"><span data-stu-id="0e683-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="0e683-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="0e683-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="0e683-792">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="0e683-793">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="0e683-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="0e683-794">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="0e683-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="0e683-795">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* DB ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="0e683-796">テスト データで DB を初期化するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="0e683-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="0e683-797">EF Core によって空の DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="0e683-798">このセクションでは、それにテスト データを入力するために `Initialize` メソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="0e683-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="0e683-799">*Data* フォルダーで、 *DbInitializer.cs* という名前の新しいクラス ファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="0e683-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="0e683-800">メモ:上のコードでは、名前空間 (`namespace ContosoUniversity.Models`) に `Data` ではなく `Models` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="0e683-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="0e683-801">`Models` はスキャフォールディング機能によって生成されたコードと一致します。</span><span class="sxs-lookup"><span data-stu-id="0e683-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="0e683-802">詳細については、[こちらの GitHub のスキャフォールディングの問題](https://github.com/aspnet/Scaffolding/issues/822)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="0e683-803">このコードは、DB に学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="0e683-804">DB に学生が存在しない場合、テスト データを使用して DB が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="0e683-805">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="0e683-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="0e683-806">`EnsureCreated` メソッドは、DB のコンテキストに合わせて DB を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="0e683-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="0e683-807">DB が存在する場合、`EnsureCreated` は DB を変更することなく戻ってきます。</span><span class="sxs-lookup"><span data-stu-id="0e683-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="0e683-808">*Program.cs* で、`Initialize` を呼び出すように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="0e683-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="0e683-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0e683-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0e683-810">アプリが実行されている場合は停止し、 **パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0e683-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="0e683-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0e683-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0e683-812">アプリが実行されている場合は停止し、 *CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="0e683-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="0e683-813">DB を表示する</span><span class="sxs-lookup"><span data-stu-id="0e683-813">View the DB</span></span>

<span data-ttu-id="0e683-814">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="0e683-815">したがって、データベース名は "SchoolContext-{GUID}" になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="0e683-816">GUID は、ユーザーごとに異なります。</span><span class="sxs-lookup"><span data-stu-id="0e683-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="0e683-817">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="0e683-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="0e683-818">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="0e683-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="0e683-819">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="0e683-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="0e683-820">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="0e683-821">非同期コード</span><span class="sxs-lookup"><span data-stu-id="0e683-821">Asynchronous code</span></span>

<span data-ttu-id="0e683-822">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="0e683-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="0e683-823">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0e683-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="0e683-824">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="0e683-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="0e683-825">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="0e683-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="0e683-826">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="0e683-827">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="0e683-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="0e683-828">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="0e683-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="0e683-829">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="0e683-830">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="0e683-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="0e683-831">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="0e683-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="0e683-832">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="0e683-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="0e683-833">返された [Task](/dotnet/api/system.threading.tasks.task) オブジェクトを自動作成する。</span><span class="sxs-lookup"><span data-stu-id="0e683-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="0e683-834">詳細については、[Task の戻り値の型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="0e683-835">暗黙の戻り値の型 `Task` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="0e683-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="0e683-836">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="0e683-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="0e683-837">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="0e683-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="0e683-838">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="0e683-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="0e683-839">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="0e683-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="0e683-840">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="0e683-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="0e683-841">クエリやコマンドを DB に送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="0e683-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="0e683-842">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e683-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="0e683-843">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="0e683-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="0e683-844">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="0e683-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="0e683-845">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0e683-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="0e683-846">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0e683-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="0e683-847">次のチュートリアルでは、基本的な CRUD (作成、読み取り、更新、削除) の操作について説明します。</span><span class="sxs-lookup"><span data-stu-id="0e683-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="0e683-848">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0e683-848">Additional resources</span></span>

* [<span data-ttu-id="0e683-849">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="0e683-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="0e683-850">次へ</span><span class="sxs-lookup"><span data-stu-id="0e683-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
