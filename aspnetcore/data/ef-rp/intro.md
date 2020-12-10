---
title: ASP.NET Core での Entity Framework Core を使用した Razor Pages - チュートリアル 1/8
author: rick-anderson
description: Entity Framework Core を使用して Razor Pages アプリを作成する方法について説明します
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 0e81397d210518854939c6941e7f6da43ed48389
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "96855509"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="485cd-103">ASP.NET Core での Entity Framework Core を使用した Razor Pages - チュートリアル 1/8</span><span class="sxs-lookup"><span data-stu-id="485cd-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="485cd-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="485cd-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="485cd-105">これは、[ASP.NET Core Razor Pages](xref:razor-pages/index) アプリでの Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="485cd-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="485cd-106">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="485cd-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="485cd-107">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="485cd-108">このチュートリアルでは、コード ファーストのアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="485cd-109">データベース ファーストのアプローチを使用してこのチュートリアルを実行する方法の詳細については、[こちらの Github イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/16897)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="485cd-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="485cd-110">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="485cd-111">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="485cd-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="485cd-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="485cd-112">Prerequisites</span></span>

* <span data-ttu-id="485cd-113">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="485cd-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="485cd-116">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="485cd-116">Database engines</span></span>

<span data-ttu-id="485cd-117">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="485cd-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="485cd-118">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="485cd-119">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="485cd-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="485cd-120">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="485cd-120">Troubleshooting</span></span>

<span data-ttu-id="485cd-121">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="485cd-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="485cd-122">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="485cd-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="485cd-123">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="485cd-123">The sample app</span></span>

<span data-ttu-id="485cd-124">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="485cd-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="485cd-125">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="485cd-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="485cd-126">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="485cd-126">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="485cd-129">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="485cd-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="485cd-130">このチュートリアルでは、UI をカスタマイズする方法ではなく、EF Core と ASP.NET Core の使用方法について主に説明します。</span><span class="sxs-lookup"><span data-stu-id="485cd-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="485cd-131">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="485cd-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="485cd-133">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="485cd-134">**[新しいプロジェクトの作成]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="485cd-135">**[新しいプロジェクトの構成]** ダイアログで、 **[プロジェクト名]** に「`ContosoUniversity`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="485cd-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="485cd-136">コードをコピーするときに各`namespace`が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="485cd-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="485cd-137">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="485cd-137">Select **Create**.</span></span>
1. <span data-ttu-id="485cd-138">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで、次のものを選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="485cd-139">ドロップダウンで **[.NET Core]** と **[ASP.NET Core 5.0]**</span><span class="sxs-lookup"><span data-stu-id="485cd-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="485cd-140">**ASP.NET Core Web アプリ**。</span><span class="sxs-lookup"><span data-stu-id="485cd-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="485cd-141">**[作成]** 
      ![[新しい ASP.NET Core プロジェクト] ダイアログ](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="485cd-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="485cd-143">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="485cd-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="485cd-144">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="485cd-145">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="485cd-145">Set up the site style</span></span>

<span data-ttu-id="485cd-146">次のコードをコピーして、*Pages/Shared/_Layout.cshtml* ファイルに貼り付けます: </span><span class="sxs-lookup"><span data-stu-id="485cd-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="485cd-147">このレイアウト ファイルによってサイト ヘッダー、フッター、およびメニューが設定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="485cd-148">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="485cd-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="485cd-149">"ContosoUniversity" をいずれも "Contoso University" へ変更。</span><span class="sxs-lookup"><span data-stu-id="485cd-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="485cd-150">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="485cd-150">There are three occurrences.</span></span>
* <span data-ttu-id="485cd-151">**[ホーム]** および **[プライバシー]** メニュー エントリが削除されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="485cd-152">**[バージョン情報]** 、 **[学生]** 、 **[コース]** 、 **[講師]** 、および **[部門]** のエントリが追加されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="485cd-153">*Pages/Index.cshtml* で、ファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="485cd-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="485cd-154">上記のコードでは、ASP.NET Core に関するテキストがこのアプリに関するテキストに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="485cd-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="485cd-155">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="485cd-156">データ モデル</span><span class="sxs-lookup"><span data-stu-id="485cd-156">The data model</span></span>

<span data-ttu-id="485cd-157">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-157">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="485cd-159">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="485cd-160">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-160">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="485cd-162">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="485cd-163">次のコードを使用して、*Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="485cd-164">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="485cd-165">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="485cd-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="485cd-166">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="485cd-167">詳細については、[EF Core - キー](/ef/core/modeling/keys?tabs=data-annotations)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="485cd-168">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="485cd-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="485cd-169">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="485cd-170">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="485cd-171">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="485cd-172">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="485cd-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="485cd-173">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="485cd-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="485cd-174">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="485cd-175">StudentID は、Enrollment テーブルの *外部キー* です。</span><span class="sxs-lookup"><span data-stu-id="485cd-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="485cd-176">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="485cd-177">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="485cd-178">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="485cd-179">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-179">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="485cd-181">以下のコードを使用して、*Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="485cd-182">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="485cd-183">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="485cd-184">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="485cd-185">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="485cd-186">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="485cd-187">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="485cd-188">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="485cd-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="485cd-189">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="485cd-190">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="485cd-191">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="485cd-192">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="485cd-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="485cd-193">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="485cd-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="485cd-194">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="485cd-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="485cd-195">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="485cd-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="485cd-196">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="485cd-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="485cd-197">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-197">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="485cd-199">以下のコードを使用して、*Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="485cd-200">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="485cd-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="485cd-201">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="485cd-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="485cd-202">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="485cd-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="485cd-203">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="485cd-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="485cd-204">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="485cd-204">Scaffold Student pages</span></span>

<span data-ttu-id="485cd-205">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="485cd-206">EF Core `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="485cd-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="485cd-207">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="485cd-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="485cd-208">これは <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="485cd-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="485cd-209">`Student` エンティティの作成、読み取り、更新、および削除 (CRUD) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="485cd-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="485cd-211">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="485cd-212">**ソリューション エクスプローラー** で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="485cd-213">**[新しいスキャフォールディング アイテムの追加]** ダイアログで次のようにします。</span><span class="sxs-lookup"><span data-stu-id="485cd-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="485cd-214">左側のタブで、 **[インストール済み] > [共通] > [Razor Pages]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="485cd-215">**[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="485cd-216">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="485cd-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="485cd-217">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="485cd-218">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="485cd-219">データ コンテキスト名が、`ContosoUniversityContext` ではなく `SchoolContext` で終わるように変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="485cd-220">更新されたコンテキスト名: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="485cd-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="485cd-221">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="485cd-221">Select **Add**.</span></span>

<span data-ttu-id="485cd-222">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="485cd-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="485cd-224">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="485cd-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="485cd-225">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="485cd-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="485cd-226">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="485cd-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="485cd-227">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="485cd-228">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="485cd-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="485cd-229">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="485cd-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="485cd-230">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="485cd-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="485cd-231">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="485cd-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="485cd-232">前の手順が失敗した場合は、プロジェクトをビルドし、スキャフォールディング手順を再試行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="485cd-233">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="485cd-233">The scaffolding process:</span></span>

* <span data-ttu-id="485cd-234">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-234">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="485cd-235">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="485cd-236">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="485cd-237">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="485cd-238">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="485cd-239">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="485cd-240">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="485cd-241">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="485cd-242">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-242">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="485cd-243">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="485cd-243">Database connection string</span></span>

<span data-ttu-id="485cd-244">スキャフォールディング ツールを使用すると、 *appsettings.json* ファイルに接続文字列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-244">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="485cd-246">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="485cd-247">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="485cd-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="485cd-248">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="485cd-250">SQLite 接続文字列を *CU.db* に短縮します。</span><span class="sxs-lookup"><span data-stu-id="485cd-250">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="485cd-251">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="485cd-251">Update the database context class</span></span>

<span data-ttu-id="485cd-252">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="485cd-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="485cd-253">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="485cd-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="485cd-254">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="485cd-255">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="485cd-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="485cd-256">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="485cd-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="485cd-257">上記のコードは、単数形の `DbSet<Student> Student` から複数形の `DbSet<Student> Students` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="485cd-258">Razor Pages のコードが新しい `DBSet` 名と一致するようにするには、`_context.Student.` から</span><span class="sxs-lookup"><span data-stu-id="485cd-258">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="485cd-259">`_context.Students.` へとグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-259">to: `_context.Students.`</span></span>

<span data-ttu-id="485cd-260">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-260">There are 8 occurrences.</span></span>

<span data-ttu-id="485cd-261">エンティティ セットには複数のエンティティが含まれているため、開発者の多くは `DBSet` プロパティ名を複数形にすることを好みます。</span><span class="sxs-lookup"><span data-stu-id="485cd-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="485cd-262">強調表示されたコード:</span><span class="sxs-lookup"><span data-stu-id="485cd-262">The highlighted code:</span></span>

* <span data-ttu-id="485cd-263">エンティティ セットごとに [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="485cd-264">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="485cd-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="485cd-265">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="485cd-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="485cd-266">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="485cd-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="485cd-267"><xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="485cd-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="485cd-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="485cd-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="485cd-269">`SchoolContext` が初期化されたとき、ただしモデルがロックダウンされてコンテキストの初期化に使用される前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="485cd-270">チュートリアルの後半で `Student` エンティティが他のエンティティへの参照を取得することから、必須となります。</span><span class="sxs-lookup"><span data-stu-id="485cd-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="485cd-271">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="485cd-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="485cd-272">Startup.cs</span></span>

<span data-ttu-id="485cd-273">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="485cd-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="485cd-274">サービス (`SchoolContext` など) は、アプリの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="485cd-275">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-275">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="485cd-276">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="485cd-277">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="485cd-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-278">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="485cd-279">次の強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="485cd-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="485cd-281">スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="485cd-282">運用データベースの使用の詳細については、「[開発用に SQLite を、運用環境に SQL Server を使用する](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="485cd-283">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="485cd-284">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="485cd-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="485cd-285">データベース例外フィルターを追加する</span><span class="sxs-lookup"><span data-stu-id="485cd-285">Add the database exception filter</span></span>

<span data-ttu-id="485cd-286">次のコードに示すように、`ConfigureServices` に <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> を追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="485cd-288">[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="485cd-289">PMC で、次のように入力して NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="485cd-291">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet パッケージには Entity Framework Core のエラー ページ用の ASP.NET Core ミドルウェアが用意されています。</span><span class="sxs-lookup"><span data-stu-id="485cd-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="485cd-292">このミドルウェアは、Entity Framework Core の移行に関するエラーを検出して診断するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="485cd-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="485cd-293">`AddDatabaseDeveloperPageExceptionFilter` により、[開発環境](xref:fundamentals/environments)で役に立つエラー情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="485cd-294">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="485cd-294">Create the database</span></span>

<span data-ttu-id="485cd-295">データベースが存在しない場合は、*Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="485cd-296">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="485cd-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="485cd-297">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="485cd-298">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="485cd-299">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="485cd-299">Delete the database.</span></span> <span data-ttu-id="485cd-300">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="485cd-300">Any existing data is lost.</span></span>
* <span data-ttu-id="485cd-301">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-301">Change the data model.</span></span> <span data-ttu-id="485cd-302">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="485cd-303">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-303">Run the app.</span></span>
* <span data-ttu-id="485cd-304">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="485cd-305">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="485cd-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="485cd-306">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="485cd-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="485cd-307">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="485cd-308">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="485cd-309">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="485cd-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="485cd-310">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="485cd-310">Test the app</span></span>

* <span data-ttu-id="485cd-311">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-311">Run the app.</span></span>
* <span data-ttu-id="485cd-312">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="485cd-313">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="485cd-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="485cd-314">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="485cd-314">Seed the database</span></span>

<span data-ttu-id="485cd-315">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="485cd-316">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="485cd-317">次のコードを使用して、*Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="485cd-318">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="485cd-319">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="485cd-320">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="485cd-321">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="485cd-321">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-322">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="485cd-323">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="485cd-324">`Y` で応答すると、データベースが削除されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="485cd-326">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="485cd-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="485cd-327">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="485cd-327">Restart the app.</span></span>
* <span data-ttu-id="485cd-328">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="485cd-329">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="485cd-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="485cd-331">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="485cd-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="485cd-332">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="485cd-333">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="485cd-334">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="485cd-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="485cd-335">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="485cd-336">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="485cd-338">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="485cd-339">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="485cd-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="485cd-340">非同期コード</span><span class="sxs-lookup"><span data-stu-id="485cd-340">Asynchronous code</span></span>

<span data-ttu-id="485cd-341">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="485cd-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="485cd-342">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="485cd-343">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="485cd-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="485cd-344">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="485cd-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="485cd-345">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="485cd-346">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="485cd-347">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="485cd-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="485cd-348">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="485cd-349">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="485cd-350">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="485cd-351">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="485cd-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="485cd-352">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="485cd-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="485cd-353">戻り値の型 `Task` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="485cd-353">The `Task` return type represents ongoing work.</span></span>
* <span data-ttu-id="485cd-354">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="485cd-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="485cd-355">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="485cd-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="485cd-356">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="485cd-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="485cd-357">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="485cd-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="485cd-358">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="485cd-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="485cd-359">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="485cd-360">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="485cd-361">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="485cd-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="485cd-362">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="485cd-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="485cd-363">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="485cd-364">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="485cd-365">パフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="485cd-365">Performance considerations</span></span>

<span data-ttu-id="485cd-366">一般に、Web ページで任意の数の行を読み込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="485cd-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="485cd-367">クエリでは、ページングまたは制限アプローチを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="485cd-368">たとえば、上記のクエリでは `Take` を使用して、返される行を制限できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="485cd-369">ビューで大きいテーブルを列挙すると、列挙の途中でデータベース例外が発生した場合、部分的に構築された HTTP 200 応答が返される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="485cd-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> の既定値は 1024 です。</span><span class="sxs-lookup"><span data-stu-id="485cd-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="485cd-371">次のコードでは、`MaxModelBindingCollectionSize` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="485cd-372">ページングについては、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="485cd-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="485cd-373">次の手順</span><span class="sxs-lookup"><span data-stu-id="485cd-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="485cd-374">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="485cd-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="485cd-375">これは、[ASP.NET Core Razor Pages](xref:razor-pages/index) アプリでの Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="485cd-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="485cd-376">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="485cd-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="485cd-377">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="485cd-378">このチュートリアルでは、コード ファーストのアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="485cd-379">データベース ファーストのアプローチを使用してこのチュートリアルを実行する方法の詳細については、[こちらの Github イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/16897)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="485cd-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="485cd-380">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="485cd-381">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="485cd-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="485cd-382">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="485cd-382">Prerequisites</span></span>

* <span data-ttu-id="485cd-383">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="485cd-383">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-384">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="485cd-386">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="485cd-386">Database engines</span></span>

<span data-ttu-id="485cd-387">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="485cd-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="485cd-388">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="485cd-389">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="485cd-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="485cd-390">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="485cd-390">Troubleshooting</span></span>

<span data-ttu-id="485cd-391">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="485cd-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="485cd-392">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="485cd-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="485cd-393">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="485cd-393">The sample app</span></span>

<span data-ttu-id="485cd-394">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="485cd-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="485cd-395">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="485cd-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="485cd-396">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="485cd-396">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="485cd-399">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="485cd-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="485cd-400">このチュートリアルでは、UI をカスタマイズする方法ではなく、主に EF Core の使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="485cd-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="485cd-401">ページの上部にあるリンクを使用して、完成したプロジェクトのソース コードを取得します。</span><span class="sxs-lookup"><span data-stu-id="485cd-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="485cd-402">*cu30* フォルダーには、チュートリアルの ASP.NET Core 3.0 バージョン用のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="485cd-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="485cd-403">チュートリアル 1-7 のコードの状態を反映するファイルは、*cu30snapshots* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="485cd-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="485cd-405">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="485cd-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="485cd-406">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="485cd-406">Build the project.</span></span>
* <span data-ttu-id="485cd-407">パッケージ マネージャー コンソール (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="485cd-408">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="485cd-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="485cd-410">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="485cd-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="485cd-411">*ContosoUniversity.csproj* を削除し、*ContosoUniversitySQLite.csproj* の名前を *ContosoUniversity.csproj* に変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-411">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="485cd-412">*Program.cs* で、`StartupSQLite` が使用されるように `#define Startup` をコメント アウトします。</span><span class="sxs-lookup"><span data-stu-id="485cd-412">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="485cd-413">*appSettings.json* を削除し、*appSettingsSQLite.json* の名前を *appSettings.json* に変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-413">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="485cd-414">*Migrations* フォルダーを削除し、*MigrationsSQL* の名前を *Migrations* に変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="485cd-415">`#if SQLiteVersion` のグローバル検索を実行し、`#if SQLiteVersion` と関連する `#endif` ステートメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="485cd-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="485cd-416">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="485cd-416">Build the project.</span></span>
* <span data-ttu-id="485cd-417">プロジェクト フォルダーのコマンド プロンプトで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="485cd-418">SQLite ツールで、次の SQL ステートメントを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="485cd-419">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="485cd-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="485cd-420">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="485cd-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="485cd-422">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="485cd-423">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="485cd-424">プロジェクトに *ContosoUniversity* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="485cd-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="485cd-425">コードをコピーして貼り付けるときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="485cd-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="485cd-426">ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="485cd-428">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="485cd-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="485cd-429">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-429">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="485cd-430">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="485cd-430">Set up the site style</span></span>

<span data-ttu-id="485cd-431">*Pages/Shared/_Layout.cshtml* を更新して、サイト ヘッダー、フッター、およびメニューを設定します。</span><span class="sxs-lookup"><span data-stu-id="485cd-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="485cd-432">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="485cd-433">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="485cd-433">There are three occurrences.</span></span>

* <span data-ttu-id="485cd-434">**Home** メニューと **Privacy** メニューのエントリを削除し、**About**、**Students**、**Courses**、**Instructors**、**Departments** のエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="485cd-435">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="485cd-436">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET Core に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-436">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="485cd-437">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="485cd-438">データ モデル</span><span class="sxs-lookup"><span data-stu-id="485cd-438">The data model</span></span>

<span data-ttu-id="485cd-439">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-439">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="485cd-441">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="485cd-442">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-442">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="485cd-444">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="485cd-445">次のコードを使用して、*Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="485cd-446">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="485cd-447">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="485cd-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="485cd-448">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="485cd-449">詳細については、[EF Core - キー](/ef/core/modeling/keys?tabs=data-annotations)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="485cd-450">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="485cd-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="485cd-451">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="485cd-452">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="485cd-453">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="485cd-454">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="485cd-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="485cd-455">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="485cd-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="485cd-456">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="485cd-457">StudentID は、Enrollment テーブルの *外部キー* です。</span><span class="sxs-lookup"><span data-stu-id="485cd-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="485cd-458">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="485cd-459">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="485cd-460">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="485cd-461">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-461">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="485cd-463">以下のコードを使用して、*Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="485cd-464">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="485cd-465">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="485cd-466">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="485cd-467">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="485cd-468">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="485cd-469">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="485cd-470">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="485cd-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="485cd-471">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="485cd-472">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="485cd-473">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="485cd-474">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="485cd-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="485cd-475">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="485cd-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="485cd-476">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="485cd-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="485cd-477">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="485cd-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="485cd-478">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="485cd-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="485cd-479">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-479">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="485cd-481">以下のコードを使用して、*Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="485cd-482">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="485cd-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="485cd-483">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="485cd-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="485cd-484">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="485cd-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="485cd-485">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="485cd-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="485cd-486">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="485cd-486">Scaffold Student pages</span></span>

<span data-ttu-id="485cd-487">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="485cd-488">EF Core *コンテキスト* クラス。</span><span class="sxs-lookup"><span data-stu-id="485cd-488">An EF Core *context* class.</span></span> <span data-ttu-id="485cd-489">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="485cd-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="485cd-490">これは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="485cd-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="485cd-491">`Student` エンティティの作成、読み取り、更新、および削除 (CRUD) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="485cd-491">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-492">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="485cd-493">*Pages* フォルダー内に *Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="485cd-494">**ソリューション エクスプローラー** で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-494">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="485cd-495">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-495">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="485cd-496">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="485cd-496">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="485cd-497">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="485cd-498">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="485cd-499">データ コンテキスト名を *ContosoUniversity.Models.ContosoUniversityContext* から *ContosoUniversity.Data.SchoolContext* に変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="485cd-500">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="485cd-500">Select **Add**.</span></span>

<span data-ttu-id="485cd-501">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="485cd-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="485cd-503">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="485cd-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="485cd-504">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="485cd-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="485cd-505">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="485cd-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="485cd-506">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="485cd-507">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="485cd-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="485cd-508">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="485cd-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="485cd-509">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="485cd-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="485cd-510">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="485cd-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="485cd-511">前の手順で問題が発生した場合は、プロジェクトをビルドし、スキャフォールディング ステップを再試行してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="485cd-512">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="485cd-512">The scaffolding process:</span></span>

* <span data-ttu-id="485cd-513">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-513">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="485cd-514">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="485cd-515">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="485cd-516">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="485cd-517">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="485cd-518">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="485cd-519">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="485cd-520">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="485cd-521">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-521">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="485cd-522">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="485cd-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-523">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="485cd-524">*appsettings.json* ファイルでは、接続文字列 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されています。</span><span class="sxs-lookup"><span data-stu-id="485cd-524">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="485cd-525">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="485cd-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="485cd-526">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="485cd-528">*CU.db* という名前の SQLite データベース ファイルを指すように接続文字列を変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-528">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="485cd-529">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="485cd-529">Update the database context class</span></span>

<span data-ttu-id="485cd-530">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="485cd-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="485cd-531">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="485cd-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="485cd-532">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="485cd-533">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="485cd-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="485cd-534">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="485cd-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="485cd-535">強調表示されたコードによって、各エンティティ セットの [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="485cd-536">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="485cd-536">In EF Core terminology:</span></span>

* <span data-ttu-id="485cd-537">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="485cd-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="485cd-538">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="485cd-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="485cd-539">エンティティ セットには複数のエンティティが含まれているため、DBSet プロパティは複数形の名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="485cd-540">スキャフォールディング ツールによって `Student` DBSet を作成したので、このステップでこれを複数形の `Students` に変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="485cd-541">Razor Pages のコードを新しい DBSet 名と一致させるには、プロジェクト全体で `_context.Student` を `_context.Students` にグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-541">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="485cd-542">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-542">There are 8 occurrences.</span></span>

<span data-ttu-id="485cd-543">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="485cd-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="485cd-544">Startup.cs</span></span>

<span data-ttu-id="485cd-545">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="485cd-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="485cd-546">サービス (EF Core データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="485cd-547">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-547">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="485cd-548">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="485cd-549">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="485cd-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-550">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="485cd-551">`ConfigureServices` では、強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="485cd-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="485cd-553">`ConfigureServices` では、スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="485cd-554">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="485cd-555">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="485cd-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="485cd-556">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="485cd-556">Create the database</span></span>

<span data-ttu-id="485cd-557">データベースが存在しない場合は、*Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="485cd-558">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="485cd-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="485cd-559">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="485cd-560">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="485cd-561">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="485cd-561">Delete the database.</span></span> <span data-ttu-id="485cd-562">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="485cd-562">Any existing data is lost.</span></span>
* <span data-ttu-id="485cd-563">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-563">Change the data model.</span></span> <span data-ttu-id="485cd-564">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="485cd-565">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-565">Run the app.</span></span>
* <span data-ttu-id="485cd-566">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="485cd-567">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="485cd-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="485cd-568">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="485cd-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="485cd-569">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="485cd-570">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="485cd-571">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="485cd-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="485cd-572">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="485cd-572">Test the app</span></span>

* <span data-ttu-id="485cd-573">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-573">Run the app.</span></span>
* <span data-ttu-id="485cd-574">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="485cd-575">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="485cd-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="485cd-576">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="485cd-576">Seed the database</span></span>

<span data-ttu-id="485cd-577">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="485cd-578">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="485cd-579">次のコードを使用して、*Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="485cd-580">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="485cd-581">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="485cd-582">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="485cd-583">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="485cd-583">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-584">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="485cd-585">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="485cd-587">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="485cd-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="485cd-588">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="485cd-588">Restart the app.</span></span>

* <span data-ttu-id="485cd-589">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="485cd-590">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="485cd-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-591">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="485cd-592">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="485cd-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="485cd-593">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="485cd-594">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="485cd-595">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="485cd-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="485cd-596">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="485cd-597">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="485cd-599">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="485cd-600">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="485cd-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="485cd-601">非同期コード</span><span class="sxs-lookup"><span data-stu-id="485cd-601">Asynchronous code</span></span>

<span data-ttu-id="485cd-602">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="485cd-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="485cd-603">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="485cd-604">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="485cd-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="485cd-605">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="485cd-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="485cd-606">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="485cd-607">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="485cd-608">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="485cd-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="485cd-609">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="485cd-610">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="485cd-611">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="485cd-612">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="485cd-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="485cd-613">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="485cd-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="485cd-614">戻り値の型 `Task<T>` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="485cd-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="485cd-615">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="485cd-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="485cd-616">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="485cd-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="485cd-617">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="485cd-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="485cd-618">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="485cd-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="485cd-619">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="485cd-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="485cd-620">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="485cd-621">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="485cd-622">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="485cd-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="485cd-623">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="485cd-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="485cd-624">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="485cd-625">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="485cd-626">次の手順</span><span class="sxs-lookup"><span data-stu-id="485cd-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="485cd-627">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="485cd-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="485cd-628">Contoso University のサンプル Web アプリでは、Entity Framework (EF) Core を使用して ASP.NET Core Razor Pages アプリを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="485cd-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="485cd-629">このサンプル アプリは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="485cd-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="485cd-630">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="485cd-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="485cd-631">このページは、Contoso University のサンプル アプリを作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="485cd-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="485cd-632">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="485cd-633">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="485cd-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="485cd-634">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="485cd-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-635">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="485cd-637">[Razor Pages](xref:razor-pages/index) に関する知識。</span><span class="sxs-lookup"><span data-stu-id="485cd-637">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="485cd-638">そのプログラミング経験をお持ちでない場合は、このシリーズを始める前に [Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するチュートリアルを完了してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-638">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="485cd-639">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="485cd-639">Troubleshooting</span></span>

<span data-ttu-id="485cd-640">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="485cd-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="485cd-641">ヘルプが必要なときは、[StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) について質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="485cd-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="485cd-642">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="485cd-642">The Contoso University web app</span></span>

<span data-ttu-id="485cd-643">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="485cd-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="485cd-644">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="485cd-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="485cd-645">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="485cd-645">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

<span data-ttu-id="485cd-648">このサイトの UI スタイルは、組み込みのテンプレートによって生成されるものに類似しています。</span><span class="sxs-lookup"><span data-stu-id="485cd-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="485cd-649">このチュートリアルでは、UI ではなく、EF Core と Razor ページに重点を置きます。</span><span class="sxs-lookup"><span data-stu-id="485cd-649">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="485cd-650">ContosoUniversity Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="485cd-650">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-651">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="485cd-652">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="485cd-653">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="485cd-654">プロジェクトに **ContosoUniversity** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="485cd-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="485cd-655">このプロジェクトに *ContosoUniversity* という名前を付けることは重要です。そうすることでコードをコピーしたり貼り付けるときに名前空間が一致します。</span><span class="sxs-lookup"><span data-stu-id="485cd-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="485cd-656">ドロップダウン リストで **[ASP.NET Core 2.1]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="485cd-657">前の手順の画像については、[Razor Web アプリの作成](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="485cd-657">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="485cd-658">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="485cd-660">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="485cd-660">Set up the site style</span></span>

<span data-ttu-id="485cd-661">変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="485cd-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="485cd-662">*Pages/Shared/_Layout.cshtml* に次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="485cd-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="485cd-663">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="485cd-664">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="485cd-664">There are three occurrences.</span></span>

* <span data-ttu-id="485cd-665">メニュー エントリとして「**Students**」、「**Courses**」、「**Instructors**」、「**Departments**」を追加し、「**Contact**」を削除します。</span><span class="sxs-lookup"><span data-stu-id="485cd-665">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="485cd-666">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-666">The changes are highlighted.</span></span> <span data-ttu-id="485cd-667">(マークアップは一部表示されて *いません*。)</span><span class="sxs-lookup"><span data-stu-id="485cd-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="485cd-668">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-668">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="485cd-669">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="485cd-669">Create the data model</span></span>

<span data-ttu-id="485cd-670">Contoso University アプリのエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="485cd-671">次の 3 つのエンティティから始めます。</span><span class="sxs-lookup"><span data-stu-id="485cd-671">Start with the following three entities:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="485cd-673">`Student` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="485cd-674">`Course` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="485cd-675">1 人の学生をさまざまな講座に登録できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="485cd-676">1 つの講座に任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="485cd-677">次のセクションでは、エンティティごとにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="485cd-678">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-678">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="485cd-680">*Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-680">Create a *Models* folder.</span></span> <span data-ttu-id="485cd-681">以下のコードを使用して、*Models* フォルダーで、*Student.cs* という名前のクラス ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="485cd-682">`ID` プロパティは、このクラスに相当するデータベース (DB) テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="485cd-683">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="485cd-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="485cd-684">`classnameID` の `classname` はクラスの名前です。</span><span class="sxs-lookup"><span data-stu-id="485cd-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="485cd-685">代わりの自動的に認識される主キーは、前の例の `StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="485cd-686">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="485cd-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="485cd-687">ナビゲーション プロパティは、このエンティティに関連する他のエンティティにリンクします。</span><span class="sxs-lookup"><span data-stu-id="485cd-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="485cd-688">この例では、`Student entity` の `Enrollments` プロパティによって、その `Student` に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="485cd-689">たとえば、DB 内のある Student 行に 2 つ関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="485cd-690">関連する `Enrollment` 行とは、その学生の主キー値を `StudentID` 列に含む行です。</span><span class="sxs-lookup"><span data-stu-id="485cd-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="485cd-691">たとえば、ID=1 の学生には、`Enrollment` テーブルに行が 2 つあるとします。</span><span class="sxs-lookup"><span data-stu-id="485cd-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="485cd-692">その `Enrollment` テーブルには、`StudentID` = 1 の行が 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="485cd-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="485cd-693">`StudentID` は `Enrollment` テーブルの外部キーであり、`Student` テーブルでその学生を指定します。</span><span class="sxs-lookup"><span data-stu-id="485cd-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="485cd-694">あるナビゲーション プロパティが複数のエンティティを保持できる場合、そのナビゲーション プロパティは `ICollection<T>` のようなリスト型にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="485cd-695">`ICollection<T>` を指定するか、`List<T>` や `HashSet<T>` のような型を指定できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="485cd-696">`ICollection<T>` を使用する場合、EF Core で `HashSet<T>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="485cd-697">複数のエンティティを保持するナビゲーション プロパティは、多対多または一対多の関係から発生します。</span><span class="sxs-lookup"><span data-stu-id="485cd-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="485cd-698">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-698">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="485cd-700">以下のコードを使用して、*Models* フォルダーで、*Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="485cd-701">`EnrollmentID` プロパティは主キーです。</span><span class="sxs-lookup"><span data-stu-id="485cd-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="485cd-702">このエンティティでは、`Student` エンティティのような `ID` ではなく、`classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="485cd-703">一般的に、開発者は 1 つのパターンを選択し、データ モデル全体でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="485cd-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="485cd-704">後のチュートリアルでは、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装します。</span><span class="sxs-lookup"><span data-stu-id="485cd-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="485cd-705">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="485cd-706">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="485cd-707">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="485cd-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="485cd-708">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="485cd-709">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="485cd-710">`Student` エンティティは、複数の `Enrollment` エンティティが含まれる `Student.Enrollments` ナビゲーション プロパティとは異なります。</span><span class="sxs-lookup"><span data-stu-id="485cd-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="485cd-711">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="485cd-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="485cd-712">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="485cd-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="485cd-713">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="485cd-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="485cd-714">たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーは `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="485cd-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="485cd-715">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="485cd-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="485cd-716">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="485cd-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="485cd-717">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="485cd-717">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="485cd-719">以下のコードを使用して、*Models* フォルダーで、*Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="485cd-720">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="485cd-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="485cd-721">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="485cd-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="485cd-722">`DatabaseGenerated` 属性によって、アプリで主キーを指定できます。DB に生成させるのではありません。</span><span class="sxs-lookup"><span data-stu-id="485cd-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="485cd-723">Student モデルをスキャホールディングする</span><span class="sxs-lookup"><span data-stu-id="485cd-723">Scaffold the student model</span></span>

<span data-ttu-id="485cd-724">このセクションでは、Student モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="485cd-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="485cd-725">つまり、スキャフォールディング ツールにより、Student モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="485cd-726">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="485cd-726">Build the project.</span></span>
* <span data-ttu-id="485cd-727">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-728">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="485cd-729">**ソリューション エクスプローラー** で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-729">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="485cd-730">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-730">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="485cd-731">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="485cd-731">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="485cd-732">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="485cd-733">**[データ コンテキスト クラス]** 行で **+** (+) 記号を選択し、生成された名前を **ContosoUniversity.Models.SchoolContext** に変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="485cd-734">**[データ コンテキスト クラス]** ドロップダウンで、 **[ContosoUniversity.Models.SchoolContext]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="485cd-735">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="485cd-735">Select **Add**.</span></span>

![CRUD ダイアログ](intro/_static/s1.png)

<span data-ttu-id="485cd-737">前の手順に問題がある場合は、「[ムービー モデルのスキャフォールディング](xref:tutorials/razor-pages/model#scaffold-the-movie-model)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="485cd-739">次のコマンドを実行して、Student モデルをスキャホールディングします。</span><span class="sxs-lookup"><span data-stu-id="485cd-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="485cd-740">スキャフォールディングのプロセスが作成され、次のファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="485cd-741">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="485cd-741">Files created</span></span>

* <span data-ttu-id="485cd-742">*Pages/Students* 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="485cd-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="485cd-743">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="485cd-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="485cd-744">ファイルの更新</span><span class="sxs-lookup"><span data-stu-id="485cd-744">File updates</span></span>

* <span data-ttu-id="485cd-745">*Startup.cs*:このファイルに対する変更を次のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="485cd-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="485cd-746">*appsettings.json* :ローカル データベースへの接続に使用される接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-746">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="485cd-747">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="485cd-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="485cd-748">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="485cd-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="485cd-749">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="485cd-750">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-750">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="485cd-751">db コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="485cd-752">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="485cd-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="485cd-753">*Startup.cs* の `ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="485cd-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="485cd-754">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="485cd-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="485cd-755">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="485cd-756">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="485cd-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="485cd-757">main を更新する</span><span class="sxs-lookup"><span data-stu-id="485cd-757">Update main</span></span>

<span data-ttu-id="485cd-758">*Program.cs* で、次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-758">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="485cd-759">依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="485cd-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="485cd-760">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="485cd-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="485cd-761">`EnsureCreated` メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="485cd-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="485cd-762">次は、更新された *Program.cs* ファイルのコードです。</span><span class="sxs-lookup"><span data-stu-id="485cd-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="485cd-763">`EnsureCreated` で、コンテキストのデータベースが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="485cd-764">存在する場合、アクションは何も実行されません。</span><span class="sxs-lookup"><span data-stu-id="485cd-764">If it exists, no action is taken.</span></span> <span data-ttu-id="485cd-765">存在しない場合は、データベースとそのすべてのスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="485cd-766">`EnsureCreated` は、データベースの作成に移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="485cd-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="485cd-767">`EnsureCreated` で作成されたデータベースは、後で移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="485cd-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="485cd-768">アプリの起動時に `EnsureCreated` が呼び出され、次のワークフローが可能になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="485cd-769">DB を削除します。</span><span class="sxs-lookup"><span data-stu-id="485cd-769">Delete the DB.</span></span>
* <span data-ttu-id="485cd-770">DB スキーマを変更します (たとえば、`EmailAddress` フィールドを追加します)。</span><span class="sxs-lookup"><span data-stu-id="485cd-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="485cd-771">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-771">Run the app.</span></span>
* <span data-ttu-id="485cd-772">`EnsureCreated` によって、`EmailAddress` 列を含む DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="485cd-773">`EnsureCreated` は、スキーマが急速に進化している開発の早期に便利です。</span><span class="sxs-lookup"><span data-stu-id="485cd-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="485cd-774">このチュートリアルの後半では、DB は削除され、移行が使用されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="485cd-775">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="485cd-775">Test the app</span></span>

<span data-ttu-id="485cd-776">アプリを実行し、cookie ポリシーに同意します。</span><span class="sxs-lookup"><span data-stu-id="485cd-776">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="485cd-777">このアプリで個人情報が保持されることはありません。</span><span class="sxs-lookup"><span data-stu-id="485cd-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="485cd-778">cookie ポリシーについては、[EU の一般的なデータ保護規制 (GDPR) のサポート](xref:security/gdpr)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-778">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="485cd-779">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="485cd-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="485cd-780">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="485cd-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="485cd-781">SchoolContext DB コンテキストを確認する</span><span class="sxs-lookup"><span data-stu-id="485cd-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="485cd-782">所与のデータ モデルの EF Core 機能を調整するメイン クラスは、DB コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="485cd-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="485cd-783">データ コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="485cd-784">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="485cd-785">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="485cd-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="485cd-786">次のコードを使用して *SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="485cd-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="485cd-787">強調表示されたコードによって、各エンティティ セットの [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="485cd-788">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="485cd-788">In EF Core terminology:</span></span>

* <span data-ttu-id="485cd-789">エンティティ セットは通常、DB テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="485cd-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="485cd-790">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="485cd-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="485cd-791">`DbSet<Enrollment>` と `DbSet<Course>` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="485cd-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="485cd-792">EF Core にはそれらが暗黙的に含まれます。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。</span><span class="sxs-lookup"><span data-stu-id="485cd-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="485cd-793">このチュートリアルでは、`SchoolContext` に `DbSet<Enrollment>` と `DbSet<Course>` を残します。</span><span class="sxs-lookup"><span data-stu-id="485cd-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="485cd-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="485cd-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="485cd-795">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="485cd-796">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="485cd-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="485cd-797">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="485cd-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="485cd-798">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* DB ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="485cd-799">テスト データで DB を初期化するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="485cd-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="485cd-800">EF Core によって空の DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="485cd-801">このセクションでは、それにテスト データを入力するために `Initialize` メソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="485cd-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="485cd-802">*Data* フォルダーで、*DbInitializer.cs* という名前の新しいクラス ファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="485cd-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="485cd-803">メモ:上のコードでは、名前空間 (`namespace ContosoUniversity.Models`) に `Data` ではなく `Models` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="485cd-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="485cd-804">`Models` はスキャフォールディング機能によって生成されたコードと一致します。</span><span class="sxs-lookup"><span data-stu-id="485cd-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="485cd-805">詳細については、[こちらの GitHub のスキャフォールディングの問題](https://github.com/aspnet/Scaffolding/issues/822)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="485cd-806">このコードは、DB に学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="485cd-807">DB に学生が存在しない場合、テスト データを使用して DB が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="485cd-808">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="485cd-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="485cd-809">`EnsureCreated` メソッドは、DB のコンテキストに合わせて DB を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="485cd-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="485cd-810">DB が存在する場合、`EnsureCreated` は DB を変更することなく戻ってきます。</span><span class="sxs-lookup"><span data-stu-id="485cd-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="485cd-811">*Program.cs* で、`Initialize` を呼び出すように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="485cd-811">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="485cd-812">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="485cd-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="485cd-813">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="485cd-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="485cd-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="485cd-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="485cd-815">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="485cd-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="485cd-816">DB を表示する</span><span class="sxs-lookup"><span data-stu-id="485cd-816">View the DB</span></span>

<span data-ttu-id="485cd-817">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="485cd-818">したがって、データベース名は "SchoolContext-{GUID}" になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="485cd-819">GUID は、ユーザーごとに異なります。</span><span class="sxs-lookup"><span data-stu-id="485cd-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="485cd-820">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="485cd-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="485cd-821">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="485cd-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="485cd-822">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="485cd-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="485cd-823">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="485cd-824">非同期コード</span><span class="sxs-lookup"><span data-stu-id="485cd-824">Asynchronous code</span></span>

<span data-ttu-id="485cd-825">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="485cd-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="485cd-826">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="485cd-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="485cd-827">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="485cd-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="485cd-828">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="485cd-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="485cd-829">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="485cd-830">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="485cd-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="485cd-831">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="485cd-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="485cd-832">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="485cd-833">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="485cd-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="485cd-834">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="485cd-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="485cd-835">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="485cd-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="485cd-836">返された [Task](/dotnet/api/system.threading.tasks.task) オブジェクトを自動作成する。</span><span class="sxs-lookup"><span data-stu-id="485cd-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="485cd-837">詳細については、[Task の戻り値の型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="485cd-838">暗黙の戻り値の型 `Task` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="485cd-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="485cd-839">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="485cd-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="485cd-840">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="485cd-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="485cd-841">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="485cd-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="485cd-842">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="485cd-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="485cd-843">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="485cd-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="485cd-844">クエリやコマンドを DB に送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="485cd-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="485cd-845">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="485cd-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="485cd-846">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="485cd-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="485cd-847">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="485cd-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="485cd-848">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="485cd-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="485cd-849">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="485cd-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="485cd-850">次のチュートリアルでは、基本的な CRUD (作成、読み取り、更新、削除) の操作について説明します。</span><span class="sxs-lookup"><span data-stu-id="485cd-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="485cd-851">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="485cd-851">Additional resources</span></span>

* [<span data-ttu-id="485cd-852">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="485cd-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="485cd-853">次へ</span><span class="sxs-lookup"><span data-stu-id="485cd-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
