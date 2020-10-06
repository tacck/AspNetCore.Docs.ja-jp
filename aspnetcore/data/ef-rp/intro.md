---
title: ASP.NET Core での Entity Framework Core を使用した Razor Pages - チュートリアル 1/8
author: rick-anderson
description: Entity Framework Core を使用して Razor Pages アプリを作成する方法について説明します
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
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
ms.openlocfilehash: 35a5758500ae2bc691c8d08eccb22340f9998c39
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424286"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="b40b1-103">ASP.NET Core での Entity Framework Core を使用した Razor Pages - チュートリアル 1/8</span><span class="sxs-lookup"><span data-stu-id="b40b1-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="b40b1-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b40b1-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b40b1-105">これは、[ASP.NET Core Razor Pages](xref:razor-pages/index) アプリでの Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b40b1-106">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b40b1-107">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b40b1-108">このチュートリアルでは、コード ファーストのアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="b40b1-109">データベース ファーストのアプローチを使用してこのチュートリアルを実行する方法の詳細については、[こちらの Github イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/16897)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="b40b1-110">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b40b1-111">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b40b1-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b40b1-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b40b1-112">Prerequisites</span></span>

* <span data-ttu-id="b40b1-113">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="b40b1-116">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="b40b1-116">Database engines</span></span>

<span data-ttu-id="b40b1-117">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="b40b1-118">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="b40b1-119">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b40b1-120">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="b40b1-120">Troubleshooting</span></span>

<span data-ttu-id="b40b1-121">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b40b1-122">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="b40b1-123">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="b40b1-123">The sample app</span></span>

<span data-ttu-id="b40b1-124">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="b40b1-125">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b40b1-126">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-126">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="b40b1-129">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="b40b1-130">このチュートリアルでは、UI をカスタマイズする方法ではなく、EF Core と ASP.NET Core の使用方法について主に説明します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="b40b1-131">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="b40b1-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-133">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-133">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b40b1-134">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-134">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b40b1-135">プロジェクトに *ContosoUniversity* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="b40b1-136">コードをコピーして貼り付けるときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="b40b1-137">ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 5.0]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b40b1-139">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="b40b1-140">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-140">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b40b1-141">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="b40b1-141">Set up the site style</span></span>

<span data-ttu-id="b40b1-142">次のコードをコピーして、*Pages/Shared/_Layout.cshtml* ファイルに貼り付けます: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="b40b1-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="b40b1-143">このレイアウト ファイルによってサイト ヘッダー、フッター、およびメニューが設定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="b40b1-144">上記のコードは、次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="b40b1-145">"ContosoUniversity" をいずれも "Contoso University" へ変更。</span><span class="sxs-lookup"><span data-stu-id="b40b1-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b40b1-146">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-146">There are three occurrences.</span></span>
* <span data-ttu-id="b40b1-147">**[ホーム]** および **[プライバシー]** メニュー エントリが削除されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="b40b1-148">**[バージョン情報]** 、 **[学生]** 、 **[コース]** 、 **[講師]** 、および **[部門]** のエントリが追加されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-148">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="b40b1-149">*Pages/Index.cshtml* で、ファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-149">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="b40b1-150">上記のコードでは、ASP.NET Core に関するテキストがこのアプリに関するテキストに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="b40b1-151">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="b40b1-152">データ モデル</span><span class="sxs-lookup"><span data-stu-id="b40b1-152">The data model</span></span>

<span data-ttu-id="b40b1-153">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-153">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="b40b1-155">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="b40b1-156">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-156">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="b40b1-158">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="b40b1-159">次のコードを使用して、*Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="b40b1-160">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b40b1-161">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b40b1-162">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="b40b1-163">詳細については、[EF Core - キー](/ef/core/modeling/keys?tabs=data-annotations)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="b40b1-164">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b40b1-165">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b40b1-166">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="b40b1-167">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="b40b1-168">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="b40b1-169">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="b40b1-170">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="b40b1-171">StudentID は、Enrollment テーブルの*外部キー*です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="b40b1-172">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="b40b1-173">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="b40b1-174">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="b40b1-175">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-175">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="b40b1-177">以下のコードを使用して、*Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="b40b1-178">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b40b1-179">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="b40b1-180">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="b40b1-181">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="b40b1-182">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b40b1-183">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="b40b1-184">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b40b1-185">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b40b1-186">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="b40b1-187">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b40b1-188">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b40b1-189">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b40b1-190">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b40b1-191">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b40b1-192">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="b40b1-193">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-193">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="b40b1-195">以下のコードを使用して、*Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="b40b1-196">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b40b1-197">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b40b1-198">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="b40b1-199">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="b40b1-200">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="b40b1-200">Scaffold Student pages</span></span>

<span data-ttu-id="b40b1-201">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="b40b1-202">EF Core `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="b40b1-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="b40b1-203">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="b40b1-204">これは <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="b40b1-205">`Student` エンティティの作成、読み取り、更新、および削除 (CRUD) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="b40b1-205">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-207">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="b40b1-208">**ソリューション エクスプローラー**で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-208">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b40b1-209">**[新しいスキャフォールディング アイテムの追加]** ダイアログで次のようにします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="b40b1-210">左側のタブで、 **[インストール済み] > [共通] > [Razor Pages]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-210">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="b40b1-211">**[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-211">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="b40b1-212">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="b40b1-212">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="b40b1-213">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="b40b1-214">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="b40b1-215">データ コンテキスト名が、`ContosoUniversityContext` ではなく `SchoolContext` で終わるように変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="b40b1-216">更新されたコンテキスト名: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="b40b1-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="b40b1-217">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-217">Select **Add**.</span></span>

<span data-ttu-id="b40b1-218">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b40b1-220">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="b40b1-221">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="b40b1-222">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="b40b1-223">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="b40b1-224">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="b40b1-225">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="b40b1-226">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="b40b1-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="b40b1-227">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="b40b1-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="b40b1-228">前の手順が失敗した場合は、プロジェクトをビルドし、スキャフォールディング手順を再試行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="b40b1-229">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-229">The scaffolding process:</span></span>

* <span data-ttu-id="b40b1-230">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-230">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="b40b1-231">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="b40b1-232">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="b40b1-233">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="b40b1-234">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="b40b1-235">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="b40b1-236">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-236">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="b40b1-237">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-237">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="b40b1-238">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-238">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="b40b1-239">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="b40b1-239">Database connection string</span></span>

<span data-ttu-id="b40b1-240">スキャフォールディング ツールを使用すると、*appsettings.json* ファイルに接続文字列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-240">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b40b1-242">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="b40b1-243">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b40b1-244">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b40b1-246">SQLite 接続文字列を *CU.db* に短縮します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-246">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="b40b1-247">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="b40b1-247">Update the database context class</span></span>

<span data-ttu-id="b40b1-248">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b40b1-249">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b40b1-250">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b40b1-251">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b40b1-252">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="b40b1-253">上記のコードは、単数形の `DbSet<Student> Student` から複数形の `DbSet<Student> Students` に変更されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="b40b1-254">Razor Pages のコードが新しい `DBSet` 名と一致するようにするには、`_context.Student.` から</span><span class="sxs-lookup"><span data-stu-id="b40b1-254">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="b40b1-255">`_context.Students.` へとグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-255">to: `_context.Students.`</span></span>

<span data-ttu-id="b40b1-256">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-256">There are 8 occurrences.</span></span>

<span data-ttu-id="b40b1-257">エンティティ セットには複数のエンティティが含まれているため、開発者の多くは `DBSet` プロパティ名を複数形にすることを好みます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="b40b1-258">強調表示されたコード:</span><span class="sxs-lookup"><span data-stu-id="b40b1-258">The highlighted code:</span></span>

* <span data-ttu-id="b40b1-259">エンティティ セットごとに [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b40b1-260">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="b40b1-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="b40b1-261">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="b40b1-262">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="b40b1-263"><xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="b40b1-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="b40b1-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="b40b1-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="b40b1-265">`SchoolContext` が初期化されたとき、ただしモデルがロックダウンされてコンテキストの初期化に使用される前に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="b40b1-266">チュートリアルの後半で `Student` エンティティが他のエンティティへの参照を取得することから、必須となります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="b40b1-267">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="b40b1-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b40b1-268">Startup.cs</span></span>

<span data-ttu-id="b40b1-269">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b40b1-270">サービス (`SchoolContext` など) は、アプリの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="b40b1-271">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-271">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="b40b1-272">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b40b1-273">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="b40b1-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b40b1-275">次の強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="b40b1-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b40b1-277">スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="b40b1-278">運用データベースの使用の詳細については、「[開発用に SQLite を、運用環境に SQL Server を使用する](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="b40b1-279">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b40b1-280">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="b40b1-281">データベース例外フィルターを追加する</span><span class="sxs-lookup"><span data-stu-id="b40b1-281">Add the database exception filter</span></span>

<span data-ttu-id="b40b1-282">次のコードに示すように、`ConfigureServices` に `AddDatabaseDeveloperPageExceptionFilter` を追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="b40b1-284">[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="b40b1-285">PMC で、次のコマンドを入力して NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="b40b1-287">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet パッケージには Entity Framework Core のエラー ページ用の ASP.NET Core ミドルウェアが用意されています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="b40b1-288">このミドルウェアは、Entity Framework Core の移行に関するエラーを検出して診断するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b40b1-289">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="b40b1-289">Create the database</span></span>

<span data-ttu-id="b40b1-290">データベースが存在しない場合は、*Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b40b1-291">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="b40b1-292">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="b40b1-293">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="b40b1-294">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-294">Delete the database.</span></span> <span data-ttu-id="b40b1-295">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-295">Any existing data is lost.</span></span>
* <span data-ttu-id="b40b1-296">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-296">Change the data model.</span></span> <span data-ttu-id="b40b1-297">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="b40b1-298">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-298">Run the app.</span></span>
* <span data-ttu-id="b40b1-299">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="b40b1-300">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="b40b1-301">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="b40b1-302">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="b40b1-303">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="b40b1-304">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b40b1-305">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b40b1-305">Test the app</span></span>

* <span data-ttu-id="b40b1-306">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-306">Run the app.</span></span>
* <span data-ttu-id="b40b1-307">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-307">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b40b1-308">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="b40b1-309">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="b40b1-309">Seed the database</span></span>

<span data-ttu-id="b40b1-310">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="b40b1-311">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="b40b1-312">次のコードを使用して、*Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="b40b1-313">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="b40b1-314">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="b40b1-315">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b40b1-316">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-316">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b40b1-318">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="b40b1-319">`Y` で応答すると、データベースが削除されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b40b1-321">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="b40b1-322">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-322">Restart the app.</span></span>
* <span data-ttu-id="b40b1-323">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="b40b1-324">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="b40b1-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-326">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b40b1-327">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="b40b1-328">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="b40b1-329">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="b40b1-330">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="b40b1-331">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b40b1-333">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="b40b1-334">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="b40b1-335">非同期コード</span><span class="sxs-lookup"><span data-stu-id="b40b1-335">Asynchronous code</span></span>

<span data-ttu-id="b40b1-336">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b40b1-337">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b40b1-338">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b40b1-339">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b40b1-340">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b40b1-341">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="b40b1-342">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b40b1-343">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b40b1-344">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="b40b1-345">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b40b1-346">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="b40b1-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b40b1-347">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="b40b1-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="b40b1-348">戻り値の型 `Task<T>` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="b40b1-349">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b40b1-350">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b40b1-351">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b40b1-352">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b40b1-353">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="b40b1-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b40b1-354">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b40b1-355">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b40b1-356">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b40b1-357">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b40b1-358">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="b40b1-359">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="b40b1-360">パフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="b40b1-360">Performance considerations</span></span>

<span data-ttu-id="b40b1-361">一般に、Web ページで任意の数の行を読み込むべきではありません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="b40b1-362">クエリでは、ページングまたは制限アプローチを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="b40b1-363">たとえば、上記のクエリでは `Take` を使用して、返される行を制限できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="b40b1-364">ビューで大きいテーブルを列挙すると、列挙の途中でデータベース例外が発生した場合、部分的に構築された HTTP 200 応答が返される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="b40b1-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> の既定値は 1024 です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="b40b1-366">次のコードでは、`MaxModelBindingCollectionSize` が設定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b40b1-367">ページングについては、このチュートリアルで後述します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b40b1-368">次の手順</span><span class="sxs-lookup"><span data-stu-id="b40b1-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b40b1-369">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="b40b1-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b40b1-370">これは、[ASP.NET Core Razor Pages](xref:razor-pages/index) アプリでの Entity Framework (EF) Core の使用方法を示す一連のチュートリアルの 1 番目です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b40b1-371">このチュートリアルでは、架空の Contoso University の Web サイトを構築します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b40b1-372">サイトには、学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b40b1-373">このチュートリアルでは、コード ファーストのアプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="b40b1-374">データベース ファーストのアプローチを使用してこのチュートリアルを実行する方法の詳細については、[こちらの Github イシュー](https://github.com/dotnet/AspNetCore.Docs/issues/16897)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="b40b1-375">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b40b1-376">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b40b1-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b40b1-377">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b40b1-377">Prerequisites</span></span>

* <span data-ttu-id="b40b1-378">Razor Pages を初めて使用する場合は、このチュートリアルを開始する前に、[Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-378">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="b40b1-381">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="b40b1-381">Database engines</span></span>

<span data-ttu-id="b40b1-382">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="b40b1-383">Visual Studio Code の手順では、クロスプラットフォーム データベース エンジンである [SQLite](https://www.sqlite.org/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="b40b1-384">SQLite の使用を選択した場合は、SQLite データベースを管理および表示するためのサードパーティ製ツール ([DB Browser for SQLite](https://sqlitebrowser.org/) など) をダウンロードしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b40b1-385">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="b40b1-385">Troubleshooting</span></span>

<span data-ttu-id="b40b1-386">解決できない問題が発生した場合は、コードを[完成したプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と比較します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b40b1-387">ヘルプが必要なときは、[ASP.NET Core タグ](https://stackoverflow.com/questions/tagged/asp.net-core)または [EF Core タグ](https://stackoverflow.com/questions/tagged/entity-framework-core)を使用して、StackOverflow.com に質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="b40b1-388">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="b40b1-388">The sample app</span></span>

<span data-ttu-id="b40b1-389">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="b40b1-390">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b40b1-391">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-391">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index30.png)

![Students 編集ページ](intro/_static/student-edit30.png)

<span data-ttu-id="b40b1-394">このサイトの UI スタイルは、組み込みのプロジェクト テンプレートに基づいています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="b40b1-395">このチュートリアルでは、UI をカスタマイズする方法ではなく、主に EF Core の使用方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="b40b1-396">ページの上部にあるリンクを使用して、完成したプロジェクトのソース コードを取得します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="b40b1-397">*cu30* フォルダーには、チュートリアルの ASP.NET Core 3.0 バージョン用のコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="b40b1-398">チュートリアル 1-7 のコードの状態を反映するファイルは、*cu30snapshots* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b40b1-400">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="b40b1-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="b40b1-401">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-401">Build the project.</span></span>
* <span data-ttu-id="b40b1-402">パッケージ マネージャー コンソール (PMC) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="b40b1-403">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b40b1-405">完成したプロジェクトをダウンロードした後にアプリを実行するには:</span><span class="sxs-lookup"><span data-stu-id="b40b1-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="b40b1-406">*ContosoUniversity.csproj* を削除し、*ContosoUniversitySQLite.csproj* の名前を *ContosoUniversity.csproj* に変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-406">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="b40b1-407">*Program.cs* で、`StartupSQLite` が使用されるように `#define Startup` をコメント アウトします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-407">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="b40b1-408">*appSettings.json* を削除し、*appSettingsSQLite.json* の名前を *appSettings.json* に変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-408">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="b40b1-409">*Migrations* フォルダーを削除し、*MigrationsSQL* の名前を *Migrations* に変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="b40b1-410">`#if SQLiteVersion` のグローバル検索を実行し、`#if SQLiteVersion` と関連する `#endif` ステートメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="b40b1-411">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-411">Build the project.</span></span>
* <span data-ttu-id="b40b1-412">プロジェクト フォルダーのコマンド プロンプトで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="b40b1-413">SQLite ツールで、次の SQL ステートメントを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="b40b1-414">プロジェクトを実行して、データベースをシードします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="b40b1-415">Web アプリプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="b40b1-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-417">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-417">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b40b1-418">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-418">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b40b1-419">プロジェクトに *ContosoUniversity* という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-419">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="b40b1-420">コードをコピーして貼り付けるときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="b40b1-421">ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 3.0]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b40b1-423">ターミナルで、プロジェクト フォルダーを作成するフォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="b40b1-424">次のコマンドを実行して、Razor Pages プロジェクトと `cd` を新しいプロジェクト フォルダー内に作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-424">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b40b1-425">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="b40b1-425">Set up the site style</span></span>

<span data-ttu-id="b40b1-426">*Pages/Shared/_Layout.cshtml* を更新して、サイト ヘッダー、フッター、およびメニューを設定します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="b40b1-427">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b40b1-428">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-428">There are three occurrences.</span></span>

* <span data-ttu-id="b40b1-429">**Home** メニューと **Privacy** メニューのエントリを削除し、**About**、**Students**、**Courses**、**Instructors**、**Departments** のエントリを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="b40b1-430">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="b40b1-431">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET Core に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-431">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="b40b1-432">アプリを実行して、ホームページが表示されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="b40b1-433">データ モデル</span><span class="sxs-lookup"><span data-stu-id="b40b1-433">The data model</span></span>

<span data-ttu-id="b40b1-434">以降のセクションでは、データ モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-434">The following sections create a data model:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="b40b1-436">学生は任意の数のコースに登録でき、コースには任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="b40b1-437">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-437">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

* <span data-ttu-id="b40b1-439">プロジェクト フォルダー内に *Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="b40b1-440">次のコードを使用して、*Models/Student.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="b40b1-441">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b40b1-442">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b40b1-443">そのため、`Student` クラスの主キーの自動的に認識される代替名は、`StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="b40b1-444">詳細については、[EF Core - キー](/ef/core/modeling/keys?tabs=data-annotations)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="b40b1-445">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b40b1-446">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b40b1-447">この例では、`Student` エンティティの `Enrollments` プロパティによって、その Student に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="b40b1-448">たとえば、データベース内のある Student 行に 2 つの関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの Enrollment エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="b40b1-449">データベースでは、StudentID 列に学生の ID 値が含まれている場合には、Enrollment 行が Student 行に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="b40b1-450">たとえば、Student 行の ID が 1 であるとします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="b40b1-451">関連する Enrollment 行の StudentID は 1 になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="b40b1-452">StudentID は、Enrollment テーブルの*外部キー*です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="b40b1-453">`Enrollments` プロパティは、複数の関連する Enrollment エンティティが存在する可能性があるため、`ICollection<Enrollment>` として定義されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="b40b1-454">他のコレクション型 (`List<Enrollment>` や `HashSet<Enrollment>` など) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="b40b1-455">`ICollection<Enrollment>` を使用する場合、EF Core で `HashSet<Enrollment>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="b40b1-456">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-456">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="b40b1-458">以下のコードを使用して、*Models/Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="b40b1-459">`EnrollmentID` プロパティは主キーです。このエンティティは、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b40b1-460">実稼働データ モデルの場合は、1 つのパターンを選択し、一貫してそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="b40b1-461">このチュートリアルでは、どちらも機能することを示すためだけに両方を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="b40b1-462">`classname` を指定せずに `ID` を使用すると、一部の種類のデータ モデルの変更の実装が容易になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="b40b1-463">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b40b1-464">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが [nullable](/dotnet/csharp/programming-guide/nullable-types/) であることを示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="b40b1-465">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b40b1-466">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b40b1-467">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="b40b1-468">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b40b1-469">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b40b1-470">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b40b1-471">たとえば、`Student` ナビゲーション プロパティの `StudentID` は外部キーです。`Student` エンティティの主キーが `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b40b1-472">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b40b1-473">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="b40b1-474">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-474">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="b40b1-476">以下のコードを使用して、*Models/Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="b40b1-477">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b40b1-478">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b40b1-479">`DatabaseGenerated` 属性によって、主キーをデータベースに生成させるのではなく、アプリで指定することできます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="b40b1-480">プロジェクトをビルドし、コンパイラ エラーがないことを検証します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="b40b1-481">Student ページのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="b40b1-481">Scaffold Student pages</span></span>

<span data-ttu-id="b40b1-482">このセクションでは、ASP.NET Core スキャフォールディング ツールを使用して、次のものを生成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="b40b1-483">EF Core *コンテキスト* クラス。</span><span class="sxs-lookup"><span data-stu-id="b40b1-483">An EF Core *context* class.</span></span> <span data-ttu-id="b40b1-484">コンテキストは、定められたデータ モデルに対し、Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="b40b1-485">これは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="b40b1-486">`Student` エンティティの作成、読み取り、更新、および削除 (CRUD) 操作を処理する Razor ページ。</span><span class="sxs-lookup"><span data-stu-id="b40b1-486">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-488">*Pages* フォルダー内に *Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="b40b1-489">**ソリューション エクスプローラー**で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-489">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b40b1-490">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-490">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="b40b1-491">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログで、次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="b40b1-491">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="b40b1-492">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="b40b1-493">**Data context class** 行で、 **+** (+) 記号を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="b40b1-494">データ コンテキスト名を *ContosoUniversity.Models.ContosoUniversityContext* から *ContosoUniversity.Data.SchoolContext* に変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="b40b1-495">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-495">Select **Add**.</span></span>

<span data-ttu-id="b40b1-496">次のパッケージが自動的にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b40b1-498">次の .NET Core CLI コマンドを実行して、必要な NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="b40b1-499">スキャフォールディングには、Microsoft.VisualStudio.Web.CodeGeneration.Design パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="b40b1-500">アプリでは SQL Server は使用されませんが、スキャフォールディング ツールには SQL Server パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="b40b1-501">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="b40b1-502">次のコマンドを実行して、[aspnet-codegenerator スキャフォールディング ツール](xref:fundamentals/tools/dotnet-aspnet-codegenerator)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="b40b1-503">次のコマンドを実行して、Student ページをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="b40b1-504">**Windows の場合**</span><span class="sxs-lookup"><span data-stu-id="b40b1-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="b40b1-505">**macOS または Linux の場合**</span><span class="sxs-lookup"><span data-stu-id="b40b1-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="b40b1-506">前の手順で問題が発生した場合は、プロジェクトをビルドし、スキャフォールディング ステップを再試行してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="b40b1-507">スキャフォールディング プロセスは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-507">The scaffolding process:</span></span>

* <span data-ttu-id="b40b1-508">*Pages/Students* フォルダーに Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-508">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="b40b1-509">*Create.cshtml* と *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="b40b1-510">*Delete.cshtml* と *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="b40b1-511">*Details.cshtml* と *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="b40b1-512">*Edit.cshtml* と *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="b40b1-513">*Index.cshtml* と *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="b40b1-514">*Data/SchoolContext.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-514">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="b40b1-515">*Startup.cs* の依存関係の挿入にコンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-515">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="b40b1-516">データベース接続文字列を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-516">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="b40b1-517">データベース接続文字列</span><span class="sxs-lookup"><span data-stu-id="b40b1-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b40b1-519">*appsettings.json* ファイルでは、接続文字列 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-519">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="b40b1-520">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b40b1-521">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b40b1-523">*CU.db* という名前の SQLite データベース ファイルを指すように接続文字列を変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-523">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="b40b1-524">データベース コンテキスト クラスを更新する</span><span class="sxs-lookup"><span data-stu-id="b40b1-524">Update the database context class</span></span>

<span data-ttu-id="b40b1-525">定められたデータ モデルの EF Core 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b40b1-526">コンテキストは、[Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b40b1-527">コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b40b1-528">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b40b1-529">次のコードを使用して *Data/SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="b40b1-530">強調表示されたコードによって、各エンティティ セットの [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b40b1-531">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="b40b1-531">In EF Core terminology:</span></span>

* <span data-ttu-id="b40b1-532">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="b40b1-533">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b40b1-534">エンティティ セットには複数のエンティティが含まれているため、DBSet プロパティは複数形の名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="b40b1-535">スキャフォールディング ツールによって `Student` DBSet を作成したので、このステップでこれを複数形の `Students` に変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="b40b1-536">Razor Pages のコードを新しい DBSet 名と一致させるには、プロジェクト全体で `_context.Student` を `_context.Students` にグローバルに変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-536">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="b40b1-537">8 回の出現があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-537">There are 8 occurrences.</span></span>

<span data-ttu-id="b40b1-538">プロジェクトをビルドし、コンパイラ エラーがないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="b40b1-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b40b1-539">Startup.cs</span></span>

<span data-ttu-id="b40b1-540">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b40b1-541">サービス (EF Core データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b40b1-542">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-542">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b40b1-543">データベース コンテキスト インスタンスを取得するコンストラクター コードは、この後のチュートリアルで示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b40b1-544">スキャフォールディング ツールにより、コンテキスト クラスが依存関係挿入コンテナーに自動的に登録されました。</span><span class="sxs-lookup"><span data-stu-id="b40b1-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-546">`ConfigureServices` では、強調表示された行がスキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="b40b1-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b40b1-548">`ConfigureServices` では、スキャフォールダーによって追加されたコードが `UseSqlite` を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="b40b1-549">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b40b1-550">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b40b1-551">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="b40b1-551">Create the database</span></span>

<span data-ttu-id="b40b1-552">データベースが存在しない場合は、*Program.cs* を更新して作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b40b1-553">コンテキストのデータベースが存在する場合、[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) メソッドは何も実行しません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="b40b1-554">データベースが存在しない場合は、データベースとスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="b40b1-555">`EnsureCreated` により、データ モデルの変更を処理するための次のワークフローが有効になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="b40b1-556">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-556">Delete the database.</span></span> <span data-ttu-id="b40b1-557">既存のデータはすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-557">Any existing data is lost.</span></span>
* <span data-ttu-id="b40b1-558">データ モデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-558">Change the data model.</span></span> <span data-ttu-id="b40b1-559">たとえば、`EmailAddress` フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="b40b1-560">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-560">Run the app.</span></span>
* <span data-ttu-id="b40b1-561">`EnsureCreated` により、新しいスキーマを使用してデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="b40b1-562">このワークフローは、データを保持する必要がない間は、スキーマが急速に進化する開発の初期段階で適切に機能します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="b40b1-563">データベースに入力されたデータを保持する必要がある場合は、状況は異なります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="b40b1-564">その場合は、移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="b40b1-565">この後のチュートリアル シリーズでは、`EnsureCreated` によって作成されたデータベースを削除し、代わりに移行を使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="b40b1-566">`EnsureCreated` によって作成されたデータベースは、移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b40b1-567">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b40b1-567">Test the app</span></span>

* <span data-ttu-id="b40b1-568">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-568">Run the app.</span></span>
* <span data-ttu-id="b40b1-569">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-569">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b40b1-570">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="b40b1-571">データベースのシード</span><span class="sxs-lookup"><span data-stu-id="b40b1-571">Seed the database</span></span>

<span data-ttu-id="b40b1-572">`EnsureCreated` メソッドは、空のデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="b40b1-573">このセクションでは、データベースにテスト データを入力するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="b40b1-574">次のコードを使用して、*Data/DbInitializer.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="b40b1-575">このコードは、データベースに学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="b40b1-576">学生が存在しない場合は、テスト データがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="b40b1-577">パフォーマンスを最適化するために、`List<T>` コレクションではなく配列にテスト データを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="b40b1-578">*Program.cs* で、`EnsureCreated` の呼び出しを `DbInitializer.Initialize` の呼び出しに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-578">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b40b1-580">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b40b1-582">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="b40b1-583">アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-583">Restart the app.</span></span>

* <span data-ttu-id="b40b1-584">Students ページを選択すると、シードされたデータが表示されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="b40b1-585">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="b40b1-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-587">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b40b1-588">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="b40b1-589">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="b40b1-590">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="b40b1-591">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="b40b1-592">**Student** テーブルを右クリックして **[コードの表示]** をクリックし、`Student` モデルが `Student` テーブル スキーマにどのようにマップされるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b40b1-594">SQLite ツールを使用して、データベース スキーマとシードされたデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="b40b1-595">データベース ファイルの名前は *CU.db* で、プロジェクト フォルダー内に配置されています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="b40b1-596">非同期コード</span><span class="sxs-lookup"><span data-stu-id="b40b1-596">Asynchronous code</span></span>

<span data-ttu-id="b40b1-597">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b40b1-598">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b40b1-599">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b40b1-600">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b40b1-601">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b40b1-602">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="b40b1-603">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b40b1-604">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b40b1-605">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="b40b1-606">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b40b1-607">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="b40b1-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b40b1-608">返される [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) オブジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="b40b1-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="b40b1-609">戻り値の型 `Task<T>` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="b40b1-610">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b40b1-611">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b40b1-612">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b40b1-613">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b40b1-614">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="b40b1-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b40b1-615">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b40b1-616">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b40b1-617">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b40b1-618">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b40b1-619">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="b40b1-620">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b40b1-621">次の手順</span><span class="sxs-lookup"><span data-stu-id="b40b1-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b40b1-622">次のチュートリアル</span><span class="sxs-lookup"><span data-stu-id="b40b1-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b40b1-623">Contoso University のサンプル Web アプリでは、Entity Framework (EF) Core を使用して ASP.NET Core Razor Pages アプリを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="b40b1-624">このサンプル アプリは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b40b1-625">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b40b1-626">このページは、Contoso University のサンプル アプリを作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="b40b1-627">完成したアプリをダウンロードまたは表示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b40b1-628">[ダウンロードの方法はこちらをご覧ください。](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b40b1-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b40b1-629">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="b40b1-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="b40b1-632">[Razor Pages](xref:razor-pages/index) に関する知識。</span><span class="sxs-lookup"><span data-stu-id="b40b1-632">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="b40b1-633">そのプログラミング経験をお持ちでない場合は、このシリーズを始める前に [Razor Pages の概要](xref:tutorials/razor-pages/razor-pages-start)に関するチュートリアルを完了してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-633">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b40b1-634">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="b40b1-634">Troubleshooting</span></span>

<span data-ttu-id="b40b1-635">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b40b1-636">ヘルプが必要なときは、[StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) について質問を投稿することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="b40b1-637">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="b40b1-637">The Contoso University web app</span></span>

<span data-ttu-id="b40b1-638">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="b40b1-639">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b40b1-640">チュートリアルで作成する画面は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-640">Here are a few of the screens created in the tutorial.</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

<span data-ttu-id="b40b1-643">このサイトの UI スタイルは、組み込みのテンプレートによって生成されるものに類似しています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="b40b1-644">このチュートリアルでは、UI ではなく、EF Core と Razor ページに重点を置きます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-644">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="b40b1-645">ContosoUniversity Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="b40b1-645">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-647">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-647">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b40b1-648">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="b40b1-649">プロジェクトに **ContosoUniversity** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-649">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="b40b1-650">このプロジェクトに *ContosoUniversity* という名前を付けることは重要です。そうすることでコードをコピーしたり貼り付けるときに名前空間が一致します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="b40b1-651">ドロップダウン リストで **[ASP.NET Core 2.1]** を選択してから、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="b40b1-652">前の手順の画像については、[Razor Web アプリの作成](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-652">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="b40b1-653">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b40b1-655">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="b40b1-655">Set up the site style</span></span>

<span data-ttu-id="b40b1-656">変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="b40b1-657">*Pages/Shared/_Layout.cshtml* に次の変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="b40b1-658">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b40b1-659">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-659">There are three occurrences.</span></span>

* <span data-ttu-id="b40b1-660">メニュー エントリとして「**Students**」、「**Courses**」、「**Instructors**」、「**Departments**」を追加し、「**Contact**」を削除します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-660">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="b40b1-661">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-661">The changes are highlighted.</span></span> <span data-ttu-id="b40b1-662">(マークアップは一部表示されて*いません*。)</span><span class="sxs-lookup"><span data-stu-id="b40b1-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="b40b1-663">*Pages/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-663">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="b40b1-664">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="b40b1-664">Create the data model</span></span>

<span data-ttu-id="b40b1-665">Contoso University アプリのエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="b40b1-666">次の 3 つのエンティティから始めます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-666">Start with the following three entities:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="b40b1-668">`Student` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="b40b1-669">`Course` エンティティと `Enrollment` エンティティの間には一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="b40b1-670">1 人の学生をさまざまな講座に登録できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="b40b1-671">1 つの講座に任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="b40b1-672">次のセクションでは、エンティティごとにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="b40b1-673">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-673">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="b40b1-675">*Models* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-675">Create a *Models* folder.</span></span> <span data-ttu-id="b40b1-676">以下のコードを使用して、*Models* フォルダーで、*Student.cs* という名前のクラス ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="b40b1-677">`ID` プロパティは、このクラスに相当するデータベース (DB) テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="b40b1-678">既定では、EF Core は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b40b1-679">`classnameID` の `classname` はクラスの名前です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="b40b1-680">代わりの自動的に認識される主キーは、前の例の `StudentID` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="b40b1-681">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b40b1-682">ナビゲーション プロパティは、このエンティティに関連する他のエンティティにリンクします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="b40b1-683">この例では、`Student entity` の `Enrollments` プロパティによって、その `Student` に関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="b40b1-684">たとえば、DB 内のある Student 行に 2 つ関連する Enrollment 行がある場合、`Enrollments` ナビゲーション プロパティにその 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="b40b1-685">関連する `Enrollment` 行とは、その学生の主キー値を `StudentID` 列に含む行です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="b40b1-686">たとえば、ID=1 の学生には、`Enrollment` テーブルに行が 2 つあるとします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="b40b1-687">その `Enrollment` テーブルには、`StudentID` = 1 の行が 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="b40b1-688">`StudentID` は `Enrollment` テーブルの外部キーであり、`Student` テーブルでその学生を指定します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="b40b1-689">あるナビゲーション プロパティが複数のエンティティを保持できる場合、そのナビゲーション プロパティは `ICollection<T>` のようなリスト型にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="b40b1-690">`ICollection<T>` を指定するか、`List<T>` や `HashSet<T>` のような型を指定できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="b40b1-691">`ICollection<T>` を使用する場合、EF Core で `HashSet<T>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="b40b1-692">複数のエンティティを保持するナビゲーション プロパティは、多対多または一対多の関係から発生します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="b40b1-693">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-693">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="b40b1-695">以下のコードを使用して、*Models* フォルダーで、*Enrollment.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="b40b1-696">`EnrollmentID` プロパティは主キーです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="b40b1-697">このエンティティでは、`Student` エンティティのような `ID` ではなく、`classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="b40b1-698">一般的に、開発者は 1 つのパターンを選択し、データ モデル全体でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="b40b1-699">後のチュートリアルでは、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="b40b1-700">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b40b1-701">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="b40b1-702">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b40b1-703">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b40b1-704">`Enrollment` エンティティは 1 つの `Student`エンティティに関連付けられますので、このプロパティには `Student` エンティティが 1 つ含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="b40b1-705">`Student` エンティティは、複数の `Enrollment` エンティティが含まれる `Student.Enrollments` ナビゲーション プロパティとは異なります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="b40b1-706">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b40b1-707">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b40b1-708">EF Core は、プロパティの名前が `<navigation property name><primary key property name>` であれば、それを外部キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b40b1-709">たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーは `ID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b40b1-710">外部キーのプロパティには `<primary key property name>` という名前を付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b40b1-711">たとえば、`CourseID` にします。`Course` エンティティの主キーが `CourseID` であるからです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="b40b1-712">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="b40b1-712">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="b40b1-714">以下のコードを使用して、*Models* フォルダーで、*Course.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="b40b1-715">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b40b1-716">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b40b1-717">`DatabaseGenerated` 属性によって、アプリで主キーを指定できます。DB に生成させるのではありません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="b40b1-718">Student モデルをスキャホールディングする</span><span class="sxs-lookup"><span data-stu-id="b40b1-718">Scaffold the student model</span></span>

<span data-ttu-id="b40b1-719">このセクションでは、Student モデルがスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="b40b1-720">つまり、スキャフォールディング ツールにより、Student モデルの作成、読み取り、更新、削除の (CRUD) 操作用のページが生成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="b40b1-721">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-721">Build the project.</span></span>
* <span data-ttu-id="b40b1-722">*Pages/Students* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b40b1-724">**ソリューション エクスプローラー**で、*Pages/Students* フォルダーを右クリックし、 **[追加]** > **[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-724">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b40b1-725">**[スキャフォールディングを追加]** ダイアログで、 **[Entity Framework を使用する Razor ページ (CRUD)]** > **[追加]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-725">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="b40b1-726">**[Add Razor Pages using Entity Framework (CRUD)]\(Entity Framework を使用して Razor Pages (CRUD) を追加する\)** ダイアログを完了します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-726">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b40b1-727">**[モデル クラス]** ドロップダウンで、 **[Student (ContosoUniversity.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="b40b1-728">**[データ コンテキスト クラス]** 行で **+** (+) 記号を選択し、生成された名前を **ContosoUniversity.Models.SchoolContext** に変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="b40b1-729">**[データ コンテキスト クラス]** ドロップダウンで、 **[ContosoUniversity.Models.SchoolContext]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="b40b1-730">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-730">Select **Add**.</span></span>

![CRUD ダイアログ](intro/_static/s1.png)

<span data-ttu-id="b40b1-732">前の手順に問題がある場合は、「[ムービー モデルのスキャフォールディング](xref:tutorials/razor-pages/model#scaffold-the-movie-model)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b40b1-734">次のコマンドを実行して、Student モデルをスキャホールディングします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="b40b1-735">スキャフォールディングのプロセスが作成され、次のファイルが変更されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="b40b1-736">作成されたファイル</span><span class="sxs-lookup"><span data-stu-id="b40b1-736">Files created</span></span>

* <span data-ttu-id="b40b1-737">*Pages/Students* 作成、削除、詳細、編集、インデックス。</span><span class="sxs-lookup"><span data-stu-id="b40b1-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="b40b1-738">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="b40b1-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="b40b1-739">ファイルの更新</span><span class="sxs-lookup"><span data-stu-id="b40b1-739">File updates</span></span>

* <span data-ttu-id="b40b1-740">*Startup.cs*:このファイルに対する変更を次のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="b40b1-741">*appsettings.json*:ローカル データベースへの接続に使用される接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-741">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b40b1-742">依存関係挿入に登録されるコンテキストを調べる</span><span class="sxs-lookup"><span data-stu-id="b40b1-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b40b1-743">ASP.NET Core には、[依存関係挿入](xref:fundamentals/dependency-injection)が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b40b1-744">サービス (EF Core DB コンテキストなど) は、アプリケーションの起動時に依存関係挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b40b1-745">これらのサービスを必要とするコンポーネント (Razor Pages など) には、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-745">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b40b1-746">db コンテキスト インスタンスを取得するコンストラクター コードは、チュートリアルの後半で示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b40b1-747">スキャフォールディング ツールが自動的に DB コンテキストを作成し、依存関係挿入コンテナーと共に登録します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b40b1-748">*Startup.cs* の `ConfigureServices` メソッドを調べます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-748">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="b40b1-749">強調表示された行は、スキャフォールダーによって追加されました。</span><span class="sxs-lookup"><span data-stu-id="b40b1-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="b40b1-750">[DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b40b1-751">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="b40b1-752">main を更新する</span><span class="sxs-lookup"><span data-stu-id="b40b1-752">Update main</span></span>

<span data-ttu-id="b40b1-753">*Program.cs* で、次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-753">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="b40b1-754">依存関係挿入コンテナーから DB コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b40b1-755">[EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="b40b1-756">`EnsureCreated` メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="b40b1-757">次は、更新された *Program.cs* ファイルのコードです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="b40b1-758">`EnsureCreated` で、コンテキストのデータベースが存在することを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="b40b1-759">存在する場合、アクションは何も実行されません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-759">If it exists, no action is taken.</span></span> <span data-ttu-id="b40b1-760">存在しない場合は、データベースとそのすべてのスキーマが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="b40b1-761">`EnsureCreated` は、データベースの作成に移行を使用しません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="b40b1-762">`EnsureCreated` で作成されたデータベースは、後で移行を使用して更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="b40b1-763">アプリの起動時に `EnsureCreated` が呼び出され、次のワークフローが可能になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="b40b1-764">DB を削除します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-764">Delete the DB.</span></span>
* <span data-ttu-id="b40b1-765">DB スキーマを変更します (たとえば、`EmailAddress` フィールドを追加します)。</span><span class="sxs-lookup"><span data-stu-id="b40b1-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="b40b1-766">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-766">Run the app.</span></span>
* <span data-ttu-id="b40b1-767">`EnsureCreated` によって、`EmailAddress` 列を含む DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="b40b1-768">`EnsureCreated` は、スキーマが急速に進化している開発の早期に便利です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="b40b1-769">このチュートリアルの後半では、DB は削除され、移行が使用されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b40b1-770">アプリのテスト</span><span class="sxs-lookup"><span data-stu-id="b40b1-770">Test the app</span></span>

<span data-ttu-id="b40b1-771">アプリを実行し、cookie ポリシーに同意します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-771">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="b40b1-772">このアプリで個人情報が保持されることはありません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="b40b1-773">cookie ポリシーについては、[EU の一般的なデータ保護規制 (GDPR) のサポート](xref:security/gdpr)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-773">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="b40b1-774">**[Students]** リンクを選択し、 **[新規作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-774">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b40b1-775">[編集]、[詳細]、および [削除] の各リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="b40b1-776">SchoolContext DB コンテキストを確認する</span><span class="sxs-lookup"><span data-stu-id="b40b1-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="b40b1-777">所与のデータ モデルの EF Core 機能を調整するメイン クラスは、DB コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="b40b1-778">データ コンテキストは [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) から派生されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b40b1-779">データ コンテキストによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b40b1-780">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b40b1-781">次のコードを使用して *SchoolContext.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="b40b1-782">強調表示されたコードによって、各エンティティ セットの [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b40b1-783">EF Core 用語で:</span><span class="sxs-lookup"><span data-stu-id="b40b1-783">In EF Core terminology:</span></span>

* <span data-ttu-id="b40b1-784">エンティティ セットは通常、DB テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="b40b1-785">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b40b1-786">`DbSet<Enrollment>` と `DbSet<Course>` は省略可能です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="b40b1-787">EF Core にはそれらが暗黙的に含まれます。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="b40b1-788">このチュートリアルでは、`SchoolContext` に `DbSet<Enrollment>` と `DbSet<Course>` を残します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="b40b1-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="b40b1-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b40b1-790">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="b40b1-791">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b40b1-792">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b40b1-793">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* DB ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="b40b1-794">テスト データで DB を初期化するコードを追加する</span><span class="sxs-lookup"><span data-stu-id="b40b1-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="b40b1-795">EF Core によって空の DB が作成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="b40b1-796">このセクションでは、それにテスト データを入力するために `Initialize` メソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="b40b1-797">*Data* フォルダーで、*DbInitializer.cs* という名前の新しいクラス ファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="b40b1-798">メモ:上のコードでは、名前空間 (`namespace ContosoUniversity.Models`) に `Data` ではなく `Models` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="b40b1-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="b40b1-799">`Models` はスキャフォールディング機能によって生成されたコードと一致します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="b40b1-800">詳細については、[こちらの GitHub のスキャフォールディングの問題](https://github.com/aspnet/Scaffolding/issues/822)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="b40b1-801">このコードは、DB に学生が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="b40b1-802">DB に学生が存在しない場合、テスト データを使用して DB が初期化されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="b40b1-803">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b40b1-804">`EnsureCreated` メソッドは、DB のコンテキストに合わせて DB を自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="b40b1-805">DB が存在する場合、`EnsureCreated` は DB を変更することなく戻ってきます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="b40b1-806">*Program.cs* で、`Initialize` を呼び出すように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-806">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="b40b1-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b40b1-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b40b1-808">アプリが実行されている場合は停止し、**パッケージ マネージャー コンソール** (PMC) で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b40b1-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b40b1-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b40b1-810">アプリが実行されている場合は停止し、*CU.db* ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="b40b1-811">DB を表示する</span><span class="sxs-lookup"><span data-stu-id="b40b1-811">View the DB</span></span>

<span data-ttu-id="b40b1-812">前に指定したコンテキスト名にダッシュと GUID が追加されて、データベース名が生成されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="b40b1-813">したがって、データベース名は "SchoolContext-{GUID}" になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="b40b1-814">GUID は、ユーザーごとに異なります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="b40b1-815">Visual Studio の **[表示]** メニューから **SQL Server オブジェクト エクスプローラー** (SSOX) を開きます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="b40b1-816">SSOX で、 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="b40b1-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="b40b1-817">**[Tables]\(テーブル\)** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="b40b1-818">**[Student]\(学生\)** テーブルを右クリックし、 **[View Data]\(データの表示\)** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="b40b1-819">非同期コード</span><span class="sxs-lookup"><span data-stu-id="b40b1-819">Asynchronous code</span></span>

<span data-ttu-id="b40b1-820">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b40b1-821">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b40b1-822">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b40b1-823">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b40b1-824">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b40b1-825">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="b40b1-826">非同期コードが実行時に発生させるオーバーヘッドは少量です。</span><span class="sxs-lookup"><span data-stu-id="b40b1-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b40b1-827">トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b40b1-828">次のコードでは、キーワード [async](/dotnet/csharp/language-reference/keywords/async)、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="b40b1-829">キーワード `async` は次のことをコンパイラに指示します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b40b1-830">メソッド本文の一部にコールバックを生成する。</span><span class="sxs-lookup"><span data-stu-id="b40b1-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b40b1-831">返された [Task](/dotnet/api/system.threading.tasks.task) オブジェクトを自動作成する。</span><span class="sxs-lookup"><span data-stu-id="b40b1-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="b40b1-832">詳細については、[Task の戻り値の型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="b40b1-833">暗黙の戻り値の型 `Task` は進行中の作業を表します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="b40b1-834">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b40b1-835">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b40b1-836">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="b40b1-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b40b1-837">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="b40b1-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b40b1-838">EF Core を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="b40b1-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b40b1-839">クエリやコマンドを DB に送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="b40b1-840">これには、`ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync`、`SaveChangesAsync` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b40b1-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b40b1-841">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="b40b1-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b40b1-842">EF Core コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b40b1-843">非同期コードのパフォーマンス上の利点を最大限に活用するには、クエリをデータベースに送信させる EF Core メソッドを (ページングなどのための) ライブラリ パッケージで呼び出す場合、そのライブラリ パッケージで非同期が利用されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="b40b1-844">.NET での非同期プログラミングの詳細については、「[非同期の概要](/dotnet/standard/async)」と「[Async および Await を使用した非同期プログラミング (C#)](/dotnet/csharp/programming-guide/concepts/async/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b40b1-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="b40b1-845">次のチュートリアルでは、基本的な CRUD (作成、読み取り、更新、削除) の操作について説明します。</span><span class="sxs-lookup"><span data-stu-id="b40b1-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="b40b1-846">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="b40b1-846">Additional resources</span></span>

* [<span data-ttu-id="b40b1-847">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="b40b1-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="b40b1-848">次へ</span><span class="sxs-lookup"><span data-stu-id="b40b1-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
