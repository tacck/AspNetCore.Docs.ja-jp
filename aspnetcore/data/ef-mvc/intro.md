---
title: 'チュートリアル: ASP.NET MVC Web アプリでの EF Core の概要'
description: このページは、Contoso University のサンプル EF/MVC アプリを作成する方法を説明するチュートリアル シリーズの 1 回目です
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 77cf1e9ad51b7044a35e1a9b2c125b0fdd91435e
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365391"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="2c1a8-103">チュートリアル: ASP.NET MVC Web アプリでの EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="2c1a8-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="2c1a8-104">作成者: [Tom Dykstra](https://github.com/tdykstra)、[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c1a8-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="2c1a8-105">Contoso University のサンプル Web アプリでは、Entity Framework (EF) Core と Visual Studio を使用して ASP.NET Core MVC Web アプリを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-105">The Contoso University sample web ap demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="2c1a8-106">このサンプル アプリは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="2c1a8-107">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="2c1a8-108">これは、Contoso University のサンプル アプリを作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2c1a8-109">前提条件</span><span class="sxs-lookup"><span data-stu-id="2c1a8-109">Prerequisites</span></span>

* <span data-ttu-id="2c1a8-110">ASP.NET Core MVC を初めて使用する場合は、これを開始する前に、「[ASP.NET Core MVC の概要](xref:tutorials/first-mvc-app/start-mvc)」チュートリアル シリーズをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="2c1a8-111">データベース エンジン</span><span class="sxs-lookup"><span data-stu-id="2c1a8-111">Database engines</span></span>

<span data-ttu-id="2c1a8-112">Visual Studio の手順では、[SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) を使用します。これは、Windows 上でのみ実行される SQL Server Express のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="2c1a8-113">問題の解決とトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2c1a8-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="2c1a8-114">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="2c1a8-115">一般的なエラーとその解決方法の一覧については、[このシリーズの最後のチュートリアルにあるトラブルシューティングのセクション](advanced.md#common-errors)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="2c1a8-116">そこで必要な答えが見つからない場合、StackOverflow.com で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) に関する質問を投稿できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="2c1a8-117">これは 10 回のチュートリアルからなるシリーズであり、いずれの回も前のチュートリアルを基盤にしています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="2c1a8-118">チュートリアルが完了したら、毎回、プロジェクトのコピーを保存するようお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="2c1a8-119">問題に遭遇したとき、前のチュートリアルから始めることができます。シリーズ全体の始めまで戻る必要がありません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="2c1a8-120">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-120">Contoso University web app</span></span>

<span data-ttu-id="2c1a8-121">このチュートリアル シリーズで作成するアプリは、大学向けの基本的な Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="2c1a8-122">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="2c1a8-123">アプリの画面のいくつかを次に示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-123">Here are a few of the screens in the app:</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="2c1a8-126">Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-126">Create web app</span></span>

* <span data-ttu-id="2c1a8-127">Visual Studio を開始し、 **[ASP.NET Core Web アプリケーション]** > **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-127">Start Visual Studio and select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="2c1a8-128">プロジェクトに `ContosoUniversity` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-128">Name the project `ContosoUniversity`.</span></span> <span data-ttu-id="2c1a8-129">コードをコピーするときに名前空間が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-129">It's important to use this exact name including capitalization, so the namespaces match when code is copied.</span></span>
* <span data-ttu-id="2c1a8-130">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="2c1a8-130">Select **Create**.</span></span>
* <span data-ttu-id="2c1a8-131">ドロップダウン リストで **[.NET Core]** と **[ASP.NET Core 5.0]** を選択してから、 **[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-131">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application (Model-View-Controller)** template.</span></span>
  <span data-ttu-id="2c1a8-132">![[新しい ASP.NET Core プロジェクト] ダイアログ](intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="2c1a8-132">![New ASP.NET Core Project dialog](intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="2c1a8-133">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-133">Set up the site style</span></span>

<span data-ttu-id="2c1a8-134">基本的な変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-134">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="2c1a8-135">*Views/Shared/_Layout.cshtml* を開き、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-135">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="2c1a8-136">各 `ContosoUniversity` を `Contoso University` に変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-136">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="2c1a8-137">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-137">There are three occurrences.</span></span>
* <span data-ttu-id="2c1a8-138">メニュー エントリとして「 **About** 」、「 **Students** 」、「 **Courses** 」、「 **Instructors** 」、「 **Departments** 」を追加し、「 **Privacy** 」メニュー エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-138">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="2c1a8-139">次のコードでは上の変更が強調表示されています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-139">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="2c1a8-140">*Views/Home/Index.cshtml* で、ファイルの内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-140">In *Views/Home/Index.cshtml* , replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="2c1a8-141">CTRL を押しながら F5 を押してプロジェクトを実行するか、メニューで **[デバッグ]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-141">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="2c1a8-142">ホーム ページに、このチュートリアルで作成されるページのタブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-142">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Contoso University のホーム ページ](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="2c1a8-144">EF Core の NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-144">EF Core NuGet packages</span></span>

<span data-ttu-id="2c1a8-145">このチュートリアルでは SQL Server を使用します。プロバイダー パッケージは [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-145">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="2c1a8-146">EF SQL Server パッケージとその依存関係 `Microsoft.EntityFrameworkCore` と `Microsoft.EntityFrameworkCore.Relational` により、EF のランタイム サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-146">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="2c1a8-147">[Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet パッケージと [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-147">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="2c1a8-148">プログラム マネージャー コンソール (PMC) で、次のコマンドを入力して NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-148">In the Program Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

<span data-ttu-id="2c1a8-149">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet パッケージには EF Core のエラー ページ用の ASP.NET Core ミドルウェアが用意されています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-149">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="2c1a8-150">このミドルウェアは、EF Core の移行に関するエラーを検出して診断するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-150">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="2c1a8-151">EF Core で利用できるその他のデータベース プロバイダーに関しては、「[データベース プロバイダー](/ef/core/providers/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-151">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="2c1a8-152">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-152">Create the data model</span></span>

<span data-ttu-id="2c1a8-153">このアプリ用に次のエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-153">The following entity classes are created for this app:</span></span>

![Course、Enrollment、Student からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="2c1a8-155">上のエンティティには、次のリレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-155">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="2c1a8-156">`Student` エンティティと `Enrollment` エンティティの間の一対多リレーションシップ。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-156">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="2c1a8-157">1 人の学生を任意の数の講座に登録できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-157">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="2c1a8-158">`Course` エンティティと `Enrollment` エンティティの間の一対多リレーションシップ。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-158">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="2c1a8-159">1 つの講座に任意の数の学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-159">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="2c1a8-160">次のセクションでは、これらの各エンティティに対してクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-160">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="2c1a8-161">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-161">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="2c1a8-163">以下のコードを使用して、 *Models* フォルダーに `Student` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-163">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-164">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー ( **PK** ) 列です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-164">The `ID` property is the primary key ( **PK** ) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="2c1a8-165">既定では、EF は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-165">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="2c1a8-166">たとえば、PK には `ID` ではなく `StudentID` という名前を付けることができます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-166">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="2c1a8-167">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-167">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="2c1a8-168">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-168">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="2c1a8-169">`Student` エンティティの `Enrollments` プロパティ:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-169">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="2c1a8-170">その `Student` エンティティに関連するすべての `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-170">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="2c1a8-171">データベースの特定の `Student` 行に、関連する `Enrollment` 行が 2 つある場合:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-171">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="2c1a8-172">その `Student` エンティティの `Enrollments` ナビゲーション プロパティには、これら 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-172">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="2c1a8-173">`Enrollment` 行には、`StudentID` 外部キー ( **FK** ) 列の学生の PK 値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-173">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key ( **FK** ) column.</span></span>

<span data-ttu-id="2c1a8-174">ナビゲーション プロパティが複数のエンティティを保持できる場合:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-174">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="2c1a8-175">型は、`ICollection<T>`、`List<T>`、`HashSet<T>` などのリストである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-175">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="2c1a8-176">エンティティの追加、削除、更新を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-176">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="2c1a8-177">多対多および一対多のナビゲーション リレーションシップは、複数のエンティティを含むことができます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-177">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="2c1a8-178">`ICollection<T>` を使用する場合、EF によって `HashSet<T>` コレクションが既定で作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-178">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="2c1a8-179">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-179">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="2c1a8-181">以下のコードを使用して、 *Models* フォルダーに `Enrollment` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-181">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-182">`EnrollmentID` プロパティは PK です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-182">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="2c1a8-183">このエンティティには、`ID` 自体ではなく `classnameID` パターンが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-183">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="2c1a8-184">`Student` エンティティには `ID` パターンが使用されていました。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-184">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="2c1a8-185">データ モデル全体で 1 つのパターンを使用することを好む開発者もいます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-185">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="2c1a8-186">このチュートリアルのバリエーションでは、どちらのパターンも使用できることが示されています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-186">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="2c1a8-187">[後のチュートリアル](inheritance.md)では、クラス名なしの `ID` を使用して、データ モデルに継承を簡単に実装する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-187">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="2c1a8-188">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-188">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="2c1a8-189">`Grade` の型宣言の後にある `?` は、`Grade` プロパティが [Null 許容](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)であることを示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-189">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="2c1a8-190">`null` という成績は、成績が 0 であるということではありません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-190">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="2c1a8-191">`null` は、成績がわからないこと、またはまだ割り当てられていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-191">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="2c1a8-192">`StudentID` プロパティは外部キー (FK) であり、それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-192">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="2c1a8-193">`Enrollment` エンティティは 1 つの `Student` エンティティに関連付けられるので、プロパティで保持できる `Student` エンティティは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-193">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="2c1a8-194">これは、複数の `Enrollment` エンティティを保持できる `Student.Enrollments` ナビゲーション プロパティとは異なります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-194">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="2c1a8-195">`CourseID` プロパティは FK で、それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-195">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="2c1a8-196">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-196">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="2c1a8-197">プロパティの名前が `<`ナビゲーション プロパティ名`><`主キー プロパティ名`>` になっている場合、それは Entity Framework により FK プロパティとして解釈されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-197">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="2c1a8-198">たとえば、`Student` ナビゲーション プロパティに対する `StudentID` (`Student` エンティティの PK は `ID` であるため)。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-198">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="2c1a8-199">FK プロパティは、`<`主キー プロパティ名`>` という名前にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-199">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="2c1a8-200">たとえば、`CourseID` (`Course` エンティティの PK が `CourseID` であるため)。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-200">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="2c1a8-201">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-201">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="2c1a8-203">以下のコードを使用して、 *Models* フォルダーに `Course` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-203">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-204">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-204">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="2c1a8-205">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-205">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="2c1a8-206">[DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) 属性については、[後のチュートリアル](complex-data-model.md)で説明されています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-206">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="2c1a8-207">この属性を使用すると、講座の PK を、データベースによって生成するのではなく、自分で入力できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-207">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="2c1a8-208">データベース コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="2c1a8-208">Create the database context</span></span>

<span data-ttu-id="2c1a8-209">定められたデータ モデルの EF 機能を調整するメイン クラスは、<xref:Microsoft.EntityFrameworkCore.DbContext> データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-209">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="2c1a8-210">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-210">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="2c1a8-211">`DbContext` 派生クラスによって、データ モデルに含めるエンティティが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-211">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="2c1a8-212">EF の一部の動作はカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-212">Some EF behaviors can be customized.</span></span> <span data-ttu-id="2c1a8-213">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-213">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="2c1a8-214">プロジェクト フォルダーで、`Data` という名前のフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-214">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="2c1a8-215">*Data* フォルダーに、次のコードで `SchoolContext` クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-215">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-216">上記のコードにより、各エンティティ セットに対する `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-216">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="2c1a8-217">EF の用語では:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-217">In EF terminology:</span></span>

* <span data-ttu-id="2c1a8-218">エンティティ セットは通常、データベース テーブルに対応します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-218">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="2c1a8-219">エンティティはテーブル内の行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-219">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2c1a8-220">`DbSet<Enrollment>` ステートメントと `DbSet<Course>` ステートメントは省略することができ、動作は同じです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-220">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="2c1a8-221">EF には、次の理由により暗黙的にこれらが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-221">EF would include them implicitly because:</span></span>

* <span data-ttu-id="2c1a8-222">`Student` エンティティでは、`Enrollment` エンティティが参照されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-222">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="2c1a8-223">`Enrollment` エンティティでは、`Course` エンティティが参照されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-223">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="2c1a8-224">データベースが作成されると、EF によって、`DbSet` プロパティと同じ名前を持つテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-224">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="2c1a8-225">コレクションのプロパティ名は通常、複数形になります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-225">Property names for collections are typically plural.</span></span> <span data-ttu-id="2c1a8-226">たとえば、`Student` ではなく `Students`。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-226">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="2c1a8-227">テーブル名を複数形にするかどうかについては、開発者の間で意見が分かれるでしょう。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-227">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="2c1a8-228">これらのチュートリアルでは、`DbContext` で単数形のテーブル名を指定することにより、既定の動作をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-228">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="2c1a8-229">そのために、最後の DbSet プロパティの後に、次の強調表示されているコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-229">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="2c1a8-230">`SchoolContext` を登録する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-230">Register the `SchoolContext`</span></span>

<span data-ttu-id="2c1a8-231">ASP.NET Core には、[依存関係挿入](../../fundamentals/dependency-injection.md)が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-231">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="2c1a8-232">EF データベース コンテキストなどのサービスは、アプリの起動時に依存関係の挿入で登録されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-232">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="2c1a8-233">これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンストラクターのパラメーターを介してこれらのサービスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-233">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="2c1a8-234">コンテキスト インスタンスを取得するコントローラー コンストラクターのコードは、このチュートリアルで後ほど示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-234">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="2c1a8-235">`SchoolContext` をサービスとして登録するには、 *Startup.cs* を開き、強調表示されている行を `ConfigureServices` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-235">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="2c1a8-236">`DbContextOptionsBuilder` オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-236">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="2c1a8-237">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-237">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="2c1a8-238">*appsettings.json* ファイルを開き、次のマークアップで示されているように接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-238">Open the *appsettings.json* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="2c1a8-239">データベース例外フィルターを追加する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-239">Add the database exception filter</span></span>

<span data-ttu-id="2c1a8-240">次のコードに示すように、`ConfigureServices` に <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> を追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-240">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

<span data-ttu-id="2c1a8-241">`AddDatabaseDeveloperPageExceptionFilter` により、[開発環境](xref:fundamentals/environments)で役に立つエラー情報が提供されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-241">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="2c1a8-242">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="2c1a8-242">SQL Server Express LocalDB</span></span>

<span data-ttu-id="2c1a8-243">この接続文字列によって [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="2c1a8-244">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="2c1a8-245">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-245">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="2c1a8-246">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* DB ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-246">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="2c1a8-247">テスト データで DB を初期化する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-247">Initialize DB with test data</span></span>

<span data-ttu-id="2c1a8-248">EF により、空のデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-248">EF creates an empty database.</span></span> <span data-ttu-id="2c1a8-249">このセクションでは、データベースが作成された後でそれにテスト データを設定するために呼び出されるメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-249">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="2c1a8-250">`EnsureCreated` メソッドを使用して、データベースを自動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-250">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="2c1a8-251">[後のチュートリアル](migrations.md)では、モデル変更の処理方法について説明します。データベースを削除し、再作成するのではなく、Code First Migrations を利用してデータベース スキーマを変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-251">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="2c1a8-252">*Data* フォルダーに、次のコードで `DbInitializer` という名前の新しいクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-252">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="2c1a8-253">上記のコードでは、データベースが存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-253">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="2c1a8-254">データベースが見つからない場合</span><span class="sxs-lookup"><span data-stu-id="2c1a8-254">If the database is not found;</span></span>
  * <span data-ttu-id="2c1a8-255">作成されて、テスト データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-255">It is created and loaded with test data.</span></span> <span data-ttu-id="2c1a8-256">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-256">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="2c1a8-257">データベースが見つかった場合は、何も行われません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-257">If the database if found, it takes no action.</span></span>

<span data-ttu-id="2c1a8-258">次のコードを使用して *Program.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-258">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="2c1a8-259">アプリの起動時に、 *Program.cs* によって次のことが行われます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-259">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="2c1a8-260">依存関係挿入コンテナーからデータベース コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-260">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="2c1a8-261">`DbInitializer.Initialize` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-261">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="2c1a8-262">次のコードで示すように、`Initialize` メソッドが完了したらコンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-262">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="2c1a8-263">アプリが初めて実行されたときは、データベースが作成され、テスト データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-263">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="2c1a8-264">データ モデルが変更されるたび:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-264">Whenever the data model changes:</span></span>

* <span data-ttu-id="2c1a8-265">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-265">Delete the database.</span></span>
* <span data-ttu-id="2c1a8-266">シード メソッドを更新し、新しいデータベースで新たに開始します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-266">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="2c1a8-267">後のチュートリアルでは、データ モデルが変更されたら、データベースを削除して作成し直すのではなく、それを更新します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-267">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="2c1a8-268">データ モデルが変更されても、データは失われません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-268">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="2c1a8-269">コントローラーとビューを作成する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-269">Create controller and views</span></span>

<span data-ttu-id="2c1a8-270">Visual Studio のスキャフォールディング エンジンを使用して、MVC コントローラーとビューを追加します。これらにより、EF を使用してクエリが実行され、データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-270">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="2c1a8-271">[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) アクションのメソッドとビューの自動作成は、スキャフォールディングと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-271">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="2c1a8-272">**ソリューション エクスプローラー** で、`Controllers` フォルダーを右クリックし、 **[追加] > [新規スキャフォールディング アイテム]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-272">In **Solution Explorer** , right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="2c1a8-273">**[スキャフォールディングを追加]** ダイアログ ボックスで:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-273">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="2c1a8-274">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-274">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="2c1a8-275">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-275">Click **Add**.</span></span> <span data-ttu-id="2c1a8-276">**[Add MVC Controller with views, using Entity Framework]\(Entity Framework を使用したビューがある MVC コントローラーを追加する\)** ダイアログ ボックスが表示されます。![Student のスキャフォールディング](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="2c1a8-276">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="2c1a8-277">**[モデル クラス]** で **Student** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-277">In **Model class** , select **Student**.</span></span>
  * <span data-ttu-id="2c1a8-278">**[データ コンテキスト クラス]** で **SchoolContext** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-278">In **Data context class** , select **SchoolContext**.</span></span>
  * <span data-ttu-id="2c1a8-279">名前は **StudentsController** をそのまま選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-279">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="2c1a8-280">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-280">Click **Add**.</span></span>

<span data-ttu-id="2c1a8-281">Visual Studio スキャフォールディング エンジンにより、`StudentsController.cs` ファイルと、コントローラーで動作するビューのセット (`*.cshtml` ファイル) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-281">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="2c1a8-282">コントローラーによりコンストラクター パラメーターとして `SchoolContext` が受け取られることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-282">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="2c1a8-283">ASP.NET Core 依存関係挿入では、`SchoolContext` のインスタンスがコントローラーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-283">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="2c1a8-284">`Startup` クラスでそれを構成しました。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-284">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="2c1a8-285">コントローラーには `Index` アクション メソッドが含まれます。これはデータベースにあるすべての学生を表示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-285">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="2c1a8-286">このメソッドはデータベース コンテキスト インスタンスの `Students` プロパティを読み取り、Students エンティティ セットから学生の一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-286">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="2c1a8-287">このコードの非同期プログラミング要素については、チュートリアルで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-287">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="2c1a8-288">*Views/Students/Index.cshtml* ビューには、この一覧で表形式で表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-288">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="2c1a8-289">CTRL を押しながら F5 を押してプロジェクトを実行するか、メニューで **[デバッグ]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-289">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="2c1a8-290">[Students] タブをクリックすると、`DbInitializer.Initialize` メソッドによって挿入されたテスト データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-290">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="2c1a8-291">ブラウザーのウィンドウ幅によって決まることですが、`Students` タブ リンクはページの一番上に表示されるか、右上隅のナビゲーション アイコンをクリックしないと表示されません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-291">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University のホーム ページ (ウィンドウ幅が狭いとき)](intro/_static/home-page-narrow.png)

![Students インデックス ページ](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="2c1a8-294">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-294">View the database</span></span>

<span data-ttu-id="2c1a8-295">アプリが起動すると、`DbInitializer.Initialize` メソッドによって `EnsureCreated` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-295">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="2c1a8-296">EF により、データベースが存在しないことが認識されました。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-296">EF saw that there was no database:</span></span>

* <span data-ttu-id="2c1a8-297">そのため、データベースが作成されました。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-297">So it created a database.</span></span>
* <span data-ttu-id="2c1a8-298">`Initialize` メソッドのコードにより、データベースにデータが設定されました。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-298">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="2c1a8-299">Visual Studio でデータベースを表示するには、 **SQL Server オブジェクト エクスプローラー** (SSOX) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-299">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="2c1a8-300">Visual Studio の **[表示]** メニューで **[SQL Server オブジェクト エクスプローラー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-300">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="2c1a8-301">SSOX で、 **(localdb)\MSSQLLocalDB > [データベース]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-301">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="2c1a8-302">`ContosoUniversity1` を選択します。これは、 *appsettings.json* ファイル内の接続文字列に含まれるデータベース名のエントリです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-302">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="2c1a8-303">**[テーブル]** ノードを展開し、データベースのテーブルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-303">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX のテーブル](intro/_static/ssox-tables.png)

<span data-ttu-id="2c1a8-305">**Student** テーブルを右クリックし、 **[データの表示]** をクリックして、テーブル内のデータを表示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-305">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![SSOX の Student テーブル](intro/_static/ssox-student-table.png)

<span data-ttu-id="2c1a8-307">`*.mdf` データベース ファイルと `*.ldf` データベース ファイルは *C:\Users\\\<username>* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-307">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="2c1a8-308">アプリの起動時に実行される初期化メソッドで `EnsureCreated` が呼び出されるため、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-308">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="2c1a8-309">`Student` クラスに対する変更を行います。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-309">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="2c1a8-310">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-310">Delete the database.</span></span>
* <span data-ttu-id="2c1a8-311">アプリを停止してから開始します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-311">Stop, then start the app.</span></span> <span data-ttu-id="2c1a8-312">変更に合わせてデータベースが自動的に再作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-312">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="2c1a8-313">たとえば、`Student` クラスに `EmailAddress` プロパティが追加された場合、再作成されたテーブルには新しい `EmailAddress` 列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-313">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="2c1a8-314">ビュー クラスには、新しい `EmailAddress` プロパティは表示されません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-314">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="2c1a8-315">規約</span><span class="sxs-lookup"><span data-stu-id="2c1a8-315">Conventions</span></span>

<span data-ttu-id="2c1a8-316">EF によって使用される規約のため、EF で完全なデータベースが作成されるために記述するコードの量は最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-316">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="2c1a8-317">`DbSet` プロパティの名前がテーブル名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-317">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="2c1a8-318">`DbSet` プロパティによって参照されないエンティティについては、エンティティ クラス名がテーブル名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-318">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="2c1a8-319">列名には、エンティティ プロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-319">Entity property names are used for column names.</span></span>
* <span data-ttu-id="2c1a8-320">`ID` または `classnameID` という名前のエンティティ プロパティは、PK プロパティとして認識されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-320">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="2c1a8-321">名前が `<`ナビゲーション プロパティ名`><`PK プロパティ名`>` であるプロパティは、FK プロパティとして解釈されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-321">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="2c1a8-322">たとえば、`Student` ナビゲーション プロパティに対する `StudentID` (`Student` エンティティの PK は `ID` であるため)。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-322">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="2c1a8-323">FK プロパティは、`<`主キー プロパティ名`>` という名前にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-323">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="2c1a8-324">たとえば、`EnrollmentID` (`Enrollment` エンティティの PK が `EnrollmentID` であるため)。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-324">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="2c1a8-325">従来の動作をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-325">Conventional behavior can be overridden.</span></span> <span data-ttu-id="2c1a8-326">たとえば、このチュートリアルで先に示したように、テーブル名を明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-326">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="2c1a8-327">列名と任意のプロパティを、PK または FK として設定できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-327">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="2c1a8-328">非同期コード</span><span class="sxs-lookup"><span data-stu-id="2c1a8-328">Asynchronous code</span></span>

<span data-ttu-id="2c1a8-329">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-329">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="2c1a8-330">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-330">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="2c1a8-331">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-331">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="2c1a8-332">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-332">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="2c1a8-333">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-333">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="2c1a8-334">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-334">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="2c1a8-335">非同期コードは実行時に少量のオーバーヘッドを発生させるが、トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-335">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="2c1a8-336">次のコードでは、`async`、`Task<T>`、`await`、`ToListAsync` により、コードは非同期的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-336">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="2c1a8-337">キーワード `async` は、メソッド本文の一部にコールバックを生成し、返された `Task<IActionResult>` オブジェクトを自動作成するようにコンパイラに伝えます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-337">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="2c1a8-338">戻り値の型 `Task<IActionResult>` は、進行中の作業と型 `IActionResult` の結果を表します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-338">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="2c1a8-339">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-339">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="2c1a8-340">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-340">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="2c1a8-341">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-341">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="2c1a8-342">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-342">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="2c1a8-343">EF を使用する非同期コードを記述するときに注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-343">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="2c1a8-344">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-344">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="2c1a8-345">たとえば、`ToListAsync`、`SingleOrDefaultAsync`、`SaveChangesAsync` などです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-345">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="2c1a8-346">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-346">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="2c1a8-347">EF コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-347">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="2c1a8-348">非同期 EF メソッドを呼び出すとき、`await` キーワードを常に使用します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-348">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="2c1a8-349">非同期コードのパフォーマンス上の利点を利用するには、使用されているライブラリ パッケージで、クエリがデータベースに送信される EF のメソッドが呼び出されている場合は、それらでも非同期を使用するようにします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-349">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="2c1a8-350">.NET の非同期プログラミングについては、「[非同期の概要](/dotnet/articles/standard/async)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-350">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="2c1a8-351">フェッチされるエンティティを制限する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-351">Limit entities fetched</span></span>

<span data-ttu-id="2c1a8-352">クエリから返される数値またはエンティティの制限については、「[パフォーマンスに関する考慮事項](xref:data/ef-rp/intro)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-352">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="2c1a8-353">基本的な CRUD (作成、読み取り、更新、削除) 操作の実行方法を学習するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-353">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2c1a8-354">基本 CRUD 機能を実装する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-354">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="2c1a8-355">Contoso University のサンプル Web アプリケーションでは、Entity Framework (EF) Core 2.2 と Visual Studio 2017 または 2019 を使用して ASP.NET Core 2.2 MVC Web アプリケーションを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-355">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="2c1a8-356">サンプル アプリケーションは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-356">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="2c1a8-357">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-357">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="2c1a8-358">これは、Contoso University のサンプル アプリケーションを一から作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-358">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2c1a8-359">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2c1a8-359">Prerequisites</span></span>

* [<span data-ttu-id="2c1a8-360">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="2c1a8-360">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="2c1a8-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と次のワークロード:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-361">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="2c1a8-362">**ASP.NET および Web の開発** ワークロード</span><span class="sxs-lookup"><span data-stu-id="2c1a8-362">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="2c1a8-363">**.NET Core クロスプラットフォームの開発** ワークロード</span><span class="sxs-lookup"><span data-stu-id="2c1a8-363">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2c1a8-364">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="2c1a8-364">Troubleshooting</span></span>

<span data-ttu-id="2c1a8-365">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-365">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="2c1a8-366">一般的なエラーとその解決方法の一覧については、[このシリーズの最後のチュートリアルにあるトラブルシューティングのセクション](advanced.md#common-errors)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-366">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="2c1a8-367">そこで必要な答えが見つからない場合、StackOverflow.com で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) に関する質問を投稿できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-367">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="2c1a8-368">これは 10 回のチュートリアルからなるシリーズであり、いずれの回も前のチュートリアルを基盤にしています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-368">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="2c1a8-369">チュートリアルが完了したら、毎回、プロジェクトのコピーを保存するようお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-369">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="2c1a8-370">問題に遭遇したとき、前のチュートリアルから始めることができます。シリーズ全体の始めまで戻る必要がありません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-370">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="2c1a8-371">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-371">Contoso University web app</span></span>

<span data-ttu-id="2c1a8-372">一連のチュートリアルで作成するアプリケーションは、簡単な大学向け Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-372">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="2c1a8-373">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="2c1a8-374">次のような画面をこれから作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-374">Here are a few of the screens you'll create.</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="2c1a8-377">Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-377">Create web app</span></span>

* <span data-ttu-id="2c1a8-378">Visual Studio を開きます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-378">Open Visual Studio.</span></span>

* <span data-ttu-id="2c1a8-379">**[ファイル]** メニューで **[新規作成]、[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-379">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="2c1a8-380">左側のウィンドウで、 **[インストール済み]、[Visual C#]、[Web]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-380">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="2c1a8-381">**[ASP.NET Core Web アプリケーション]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-381">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="2c1a8-382">名前に「 **ContosoUniversity** 」と入力し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-382">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![[新しいプロジェクト] ダイアログ](intro/_static/new-project2.png)

* <span data-ttu-id="2c1a8-384">**[新しい ASP.NET Core Web アプリケーション]** ダイアログが表示されるのを待ちます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-384">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="2c1a8-385">**[.NET Core]** 、 **[ASP.NET Core 2.2]** 、および **[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-385">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="2c1a8-386">**[認証]** に **[認証なし]** が設定されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-386">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="2c1a8-387">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-387">Select **OK**</span></span>

  ![[新しい ASP.NET Core プロジェクト] ダイアログ](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="2c1a8-389">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-389">Set up the site style</span></span>

<span data-ttu-id="2c1a8-390">簡単な変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-390">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="2c1a8-391">*Views/Shared/_Layout.cshtml* を開き、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-391">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="2c1a8-392">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="2c1a8-393">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-393">There are three occurrences.</span></span>

* <span data-ttu-id="2c1a8-394">メニュー エントリとして「 **About** 」、「 **Students** 」、「 **Courses** 」、「 **Instructors** 」、「 **Departments** 」を追加し、「 **Privacy** 」メニュー エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-394">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="2c1a8-395">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-395">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="2c1a8-396">*Views/Home/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリケーションに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-396">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="2c1a8-397">CTRL を押しながら F5 を押してプロジェクトを実行するか、メニューで **[デバッグ]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-397">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="2c1a8-398">一連のチュートリアルで作成するページのホーム ページとタブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-398">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University のホーム ページ](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="2c1a8-400">EF Core の NuGet パッケージについて</span><span class="sxs-lookup"><span data-stu-id="2c1a8-400">About EF Core NuGet packages</span></span>

<span data-ttu-id="2c1a8-401">プロジェクトに EF Core サポートを追加するには、対象とするデータベース プロバイダーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-401">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="2c1a8-402">このチュートリアルでは SQL Server を使用します。プロバイダー パッケージは [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-402">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="2c1a8-403">このパッケージは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれているので、パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-403">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="2c1a8-404">EF SQL Server パッケージとその依存関係 (`Microsoft.EntityFrameworkCore` と `Microsoft.EntityFrameworkCore.Relational`) により、EF のランタイム サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-404">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="2c1a8-405">後の[移行](migrations.md)チュートリアルでツール パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-405">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="2c1a8-406">Entity Framework Core で利用できるその他のデータベース プロバイダーに関しては、「[データベース プロバイダー](/ef/core/providers/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-406">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="2c1a8-407">データ モデルの作成</span><span class="sxs-lookup"><span data-stu-id="2c1a8-407">Create the data model</span></span>

<span data-ttu-id="2c1a8-408">次に、Contoso University アプリケーションのエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-408">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="2c1a8-409">次の 3 つのエンティティから始めます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-409">You'll start with the following three entities.</span></span>

![講座、登録、学生からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="2c1a8-411">`Student` エンティティと `Enrollment` エンティティの間に一対多の関係があり、`Course` エンティティと `Enrollment` エンティティの間に一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-411">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="2c1a8-412">言い換えると、1 人の学生をさまざまな講座に登録し、1 つの講座にたくさんの学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-412">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="2c1a8-413">次のセクションでは、エンティティごとにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-413">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="2c1a8-414">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-414">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="2c1a8-416">*[Models]* フォルダーで、 *Student.cs* という名前のクラス ファイルを作成し、テンプレート コードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-416">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-417">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-417">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="2c1a8-418">既定では、Entity Framework は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-418">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="2c1a8-419">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-419">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="2c1a8-420">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-420">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="2c1a8-421">この例では、`Student entity` の `Enrollments` プロパティで、その `Student` エンティティに関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-421">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="2c1a8-422">つまり、データベースの `Student` 行に関連する `Enrollment` 行 (StudentID 外部キー列にその学生の主キー値が含まれる行) が 2 つある場合、その `Student` エンティティの `Enrollments` ナビゲーション プロパティには、それら 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-422">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="2c1a8-423">ナビゲーション プロパティに複数のエンティティが含まれる場合 (多対多または一対多の関係で)、その型はリストにする必要があります。`ICollection<T>` のように、エンティティを追加、削除、更新できるリストです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-423">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="2c1a8-424">`ICollection<T>`、または `List<T>` や `HashSet<T>` などの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-424">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="2c1a8-425">`ICollection<T>` を指定した場合、EF では既定で `HashSet<T>` コレクションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-425">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="2c1a8-426">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-426">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="2c1a8-428">*[Models]* フォルダーで、 *Enrollment.cs* を作成し、既存のコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-428">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-429">`EnrollmentID` プロパティは主キーになります。このエンティティは、`Student` エンティティと同様に、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-429">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="2c1a8-430">通常、パターンを 1 つ選択し、データ モデル全体でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-430">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="2c1a8-431">ここのバリエーションから、いずれのパターンも利用できることがわかります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-431">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="2c1a8-432">[後のチュートリアル](inheritance.md)では、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装する方法を学習します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-432">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="2c1a8-433">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-433">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="2c1a8-434">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-434">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="2c1a8-435">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-435">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="2c1a8-436">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-436">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="2c1a8-437">`Enrollment` エンティティは 1 つの `Student` エンティティに関連付けられており、1 つの `Student` エンティティだけを保持できます (先に見た、複数の `Enrollment` エンティティを保持できる `Student.Enrollments` ナビゲーション プロパティとは異なります)。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-437">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="2c1a8-438">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-438">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="2c1a8-439">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-439">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="2c1a8-440">Entity Framework は `<navigation property name><primary key property name>` という名前が付いている場合、プロパティを外部キー プロパティとして解釈します。たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーが `ID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-440">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="2c1a8-441">外部キーにも `<primary key property name>` という単純な名前を付けることができます。たとえば、`CourseID` です。`Course` エンティティの主キーが `CourseID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-441">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="2c1a8-442">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-442">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="2c1a8-444">*[Models]* フォルダーで、 *Course.cs* を作成し、既存のコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-444">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-445">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-445">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="2c1a8-446">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-446">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="2c1a8-447">`DatabaseGenerated` 属性については、このシリーズの[後のチュートリアル](complex-data-model.md)で詳しく学習します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-447">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="2c1a8-448">基本的に、この属性によって、講座の主キーをデータベースに生成させず、自分で入力できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-448">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="2c1a8-449">データベース コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="2c1a8-449">Create the database context</span></span>

<span data-ttu-id="2c1a8-450">所与のデータ モデルの Entity Framework 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-450">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="2c1a8-451">このクラスは、`Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-451">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="2c1a8-452">自分のコードでは、データ モデルに含めるエンティティを自分で指定します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-452">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="2c1a8-453">Entity Framework の特定の動作をカスタマイズすることもできます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-453">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="2c1a8-454">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-454">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="2c1a8-455">プロジェクト フォルダーで、 *Data* という名前のフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-455">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="2c1a8-456">*[Data]* フォルダーで、 *SchoolContext.cs* という名前の新しいクラス ファイルを作成し、テンプレート コードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-456">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-457">このコードによって、エンティティ セットごとに `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-457">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="2c1a8-458">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-458">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="2c1a8-459">`DbSet<Enrollment>` ステートメントと `DbSet<Course>` ステートメントは省略しても同じ動作をします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-459">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="2c1a8-460">Entity Framework にはそれらが暗黙的に含まれることがあります。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-460">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="2c1a8-461">データベースが作成されると、EF によって、`DbSet` プロパティと同じ名前を持つテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-461">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="2c1a8-462">一般的にコレクションのプロパティ名は複数形 (Student ではなく、Students) ですが、テーブル名を複数にするかどうかについては、開発者の間で意見が分かれています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-462">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="2c1a8-463">このチュートリアル シリーズでは、DbContext に単数のテーブル名を指定して既定の動作をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-463">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="2c1a8-464">そのために、最後の DbSet プロパティの後に、次の強調表示されているコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-464">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="2c1a8-465">コンパイラ エラーのチェックとしてプロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-465">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="2c1a8-466">SchoolContext を登録する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-466">Register the SchoolContext</span></span>

<span data-ttu-id="2c1a8-467">ASP.NET Core は既定で[依存関係の挿入](../../fundamentals/dependency-injection.md)を実装します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-467">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="2c1a8-468">サービス (EF データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-468">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="2c1a8-469">これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-469">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="2c1a8-470">このチュートリアルの後半で、コンテキスト インスタンスを取得するコントローラー コンストラクター コードが登場します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-470">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="2c1a8-471">`SchoolContext` をサービスとして登録するには、 *Startup.cs* を開き、強調表示されている行を `ConfigureServices` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-471">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="2c1a8-472">`DbContextOptionsBuilder` オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-472">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="2c1a8-473">ローカル開発の場合、 [ASP.NET Core 構成システム](xref:fundamentals/configuration/index)によって *appsettings.json* ファイルから接続文字列が読み取られます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-473">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="2c1a8-474">名前空間の `ContosoUniversity.Data` と `Microsoft.EntityFrameworkCore` に対して `using` ステートメントを追加し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-474">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="2c1a8-475">*appsettings.json* ファイルを開き、次の例で示されているように接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-475">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="2c1a8-476">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="2c1a8-476">SQL Server Express LocalDB</span></span>

<span data-ttu-id="2c1a8-477">この接続文字列によって SQL Server LocalDB データベースが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-477">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="2c1a8-478">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリケーションの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-478">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="2c1a8-479">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-479">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="2c1a8-480">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* データベース ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-480">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="2c1a8-481">テスト データで DB を初期化する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-481">Initialize DB with test data</span></span>

<span data-ttu-id="2c1a8-482">Entity Framework によって空のデータベースが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-482">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="2c1a8-483">このセクションでは、テスト データを入力する目的で、データベースの作成後に呼び出されるメソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-483">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="2c1a8-484">ここでは、データベースを自動的に作成する `EnsureCreated` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-484">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="2c1a8-485">[後のチュートリアル](migrations.md)では、モデル変更の処理方法について学習します。データベースを削除し、再作成するのではなく、Code First Migrations を利用してデータベース スキーマを変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-485">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="2c1a8-486">*[データ]* フォルダーで *DbInitializer.cs* という名前の新しいクラス ファイルを作成し、テンプレート コードを次のコードに変更します。このコードにより、必要なときにデータベースが作成され、新しいデータベースにテスト データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-486">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="2c1a8-487">このコードはデータベースに学生が存在するかどうかを確認し、存在しない場合、そのデータベースは新しく、テスト データを入力する必要があると見なします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-487">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="2c1a8-488">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-488">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="2c1a8-489">*Program.cs* で、アプリケーションの起動時に次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-489">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="2c1a8-490">依存関係挿入コンテナーからデータベース コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-490">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="2c1a8-491">seed メソッドを呼び出し、コンテキストを渡します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-491">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="2c1a8-492">seed メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-492">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="2c1a8-493">アプリケーションを初めて実行すると、データベースが作成され、テスト データが設定されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-493">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="2c1a8-494">データ モデルを変更するたび:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-494">Whenever you change the data model:</span></span>

 * <span data-ttu-id="2c1a8-495">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-495">Delete the database.</span></span>
 * <span data-ttu-id="2c1a8-496">シード メソッドを更新し、同じようにして新しいデータベースで新たに開始します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-496">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="2c1a8-497">後のチュートリアルでは、データ モデルが変わったとき、データベースを削除して作り直すのではなく、修正する方法について学習します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-497">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="2c1a8-498">コントローラーとビューを作成する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-498">Create controller and views</span></span>

<span data-ttu-id="2c1a8-499">このセクションでは、Visual Studio のスキャフォールディング エンジンを使用して、MVC のコントローラーとビューを追加します。それらにより、EF を使用して、クエリが実行され、データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-499">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="2c1a8-500">CRUD アクションのメソッドとビューの自動作成は、スキャフォールディングと言います。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-500">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="2c1a8-501">一般的には生成されたコードは修正しないのに対し、スキャフォールディングされたコードを開始点として独自の要件に合うように変更できるという点で、スキャフォールディングはコード生成と異なります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-501">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="2c1a8-502">生成されたコードをカスタマイズする必要があるとき、部分クラスを利用するか、状況が変わったときにコードを再生成します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-502">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="2c1a8-503">**ソリューション エクスプローラー** の **Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-503">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="2c1a8-504">**[スキャフォールディングを追加]** ダイアログ ボックスで:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-504">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="2c1a8-505">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-505">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="2c1a8-506">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-506">Click **Add**.</span></span> <span data-ttu-id="2c1a8-507">**[Add MVC Controller with views, using Entity Framework]\(Entity Framework を使用したビューがある MVC コントローラーを追加する\)** ダイアログ ボックスが表示されます。![Student のスキャフォールディング](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="2c1a8-507">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="2c1a8-508">**[モデル クラス]** で **[Student]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-508">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="2c1a8-509">**[データ コンテキスト クラス]** で **[SchoolContext]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-509">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="2c1a8-510">名前は **StudentsController** をそのまま選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-510">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="2c1a8-511">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-511">Click **Add**.</span></span>

<span data-ttu-id="2c1a8-512">Visual Studio スキャフォールディング エンジンにより、 *StudentsController.cs* ファイルと、コントローラーで動作するビューのセット ( *.cshtml* ファイル) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-512">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="2c1a8-513">コントローラーによりコンストラクター パラメーターとして `SchoolContext` が受け取られることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-513">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="2c1a8-514">ASP.NET Core 依存関係挿入では、`SchoolContext` のインスタンスがコントローラーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-514">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="2c1a8-515">*Startup.cs* ファイルでそれを構成しました。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-515">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="2c1a8-516">コントローラーには `Index` アクション メソッドが含まれます。これはデータベースにあるすべての学生を表示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-516">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="2c1a8-517">このメソッドはデータベース コンテキスト インスタンスの `Students` プロパティを読み取り、Students エンティティ セットから学生の一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-517">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="2c1a8-518">このコードの非同期プログラミング要素については、チュートリアルで後ほど学習します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-518">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="2c1a8-519">*Views/Students/Index.cshtml* ビューには、この一覧で表形式で表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-519">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="2c1a8-520">CTRL を押しながら F5 を押してプロジェクトを実行するか、メニューで **[デバッグ]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-520">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="2c1a8-521">[Students] タブをクリックすると、`DbInitializer.Initialize` メソッドによって挿入されたテスト データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-521">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="2c1a8-522">ブラウザーのウィンドウ幅によって決まることですが、`Students` タブ リンクはページの一番上に表示されるか、右上隅のナビゲーション アイコンをクリックしないと表示されません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-522">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University のホーム ページ (ウィンドウ幅が狭いとき)](intro/_static/home-page-narrow.png)

![Students インデックス ページ](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="2c1a8-525">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-525">View the database</span></span>

<span data-ttu-id="2c1a8-526">アプリケーションを起動する葉、`DbInitializer.Initialize` メソッドが `EnsureCreated` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-526">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="2c1a8-527">EF はデータベースがないことを認識し、作成します。`Initialize` メソッド コードの残りの部分により、データベースにデータが入力されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-527">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="2c1a8-528">**SQL Server Object Explorer** (SSOX) を利用し、Visual Studio でデータベースを表示できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-528">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="2c1a8-529">ブラウザーを閉じます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-529">Close the browser.</span></span>

<span data-ttu-id="2c1a8-530">SSOX ウィンドウがまだ開いていない場合、Visual Studio の **[表示]** メニューから選択します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-530">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="2c1a8-531">SSOX で **(localdb)\MSSQLLocalDB > [データベース]** をクリックし、 *appsettings.json* ファイルの接続文字列にあるデータベース名のエントリをクリックします。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-531">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>

<span data-ttu-id="2c1a8-532">**[テーブル]** ノードを展開し、データベースのテーブルを表示します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-532">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX のテーブル](intro/_static/ssox-tables.png)

<span data-ttu-id="2c1a8-534">**[Student]** テーブルを右クリックし、 **[データの表示]** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-534">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX の Student テーブル](intro/_static/ssox-student-table.png)

<span data-ttu-id="2c1a8-536">*.mdf* データベース ファイルと *.ldf* データベース ファイルは *C:\Users\\\<username>* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-536">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="2c1a8-537">アプリの起動時に実行される初期化子メソッドで `EnsureCreated` を呼び出すため、`Student` クラスを変更し、データベースを削除し、アプリケーションを再実行できます。変更に合わせてデータベースが自動的に再作成されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-537">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="2c1a8-538">たとえば、`Student` クラスに `EmailAddress` プロパティを追加する場合、再作成されたテーブルに新しい `EmailAddress` 列が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-538">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="2c1a8-539">規約</span><span class="sxs-lookup"><span data-stu-id="2c1a8-539">Conventions</span></span>

<span data-ttu-id="2c1a8-540">規約を利用することや Entity Framework が想定を行うことにより、Entity Framework が完全なデータベースを自動作成するために記述しなければならないコードの量が最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-540">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="2c1a8-541">`DbSet` プロパティの名前がテーブル名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-541">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="2c1a8-542">`DbSet` プロパティによって参照されないエンティティについては、エンティティ クラス名がテーブル名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-542">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="2c1a8-543">列名には、エンティティ プロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-543">Entity property names are used for column names.</span></span>
* <span data-ttu-id="2c1a8-544">ID または classnameID という名前が付けられているエンティティ プロパティは主キーのプロパティとして認識されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-544">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="2c1a8-545">*\<navigation property name>\<primary key property name>* という名前が付いている場合、プロパティは外部キー プロパティとして解釈されます。たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーが `ID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-545">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="2c1a8-546">外部キーにも *\<primary key property name>* という単純な名前を付けることができます。たとえば、`EnrollmentID` です。`Enrollment` エンティティの主キーが `EnrollmentID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-546">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="2c1a8-547">従来の動作をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-547">Conventional behavior can be overridden.</span></span> <span data-ttu-id="2c1a8-548">たとえば、このチュートリアルで先に見たように、テーブル名を明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-548">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="2c1a8-549">また、列名を設定し、任意のプロパティを主キーまたは外部キーとして設定できます。これについては、このシリーズの[後のチュートリアル](complex-data-model.md)で学習します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-549">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="2c1a8-550">非同期コード</span><span class="sxs-lookup"><span data-stu-id="2c1a8-550">Asynchronous code</span></span>

<span data-ttu-id="2c1a8-551">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-551">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="2c1a8-552">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-552">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="2c1a8-553">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-553">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="2c1a8-554">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-554">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="2c1a8-555">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-555">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="2c1a8-556">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-556">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="2c1a8-557">非同期コードは実行時に少量のオーバーヘッドを発生させるが、トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-557">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="2c1a8-558">次のコードでは、キーワード `async`、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-558">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="2c1a8-559">キーワード `async` は、メソッド本文の一部にコールバックを生成し、返された `Task<IActionResult>` オブジェクトを自動作成するようにコンパイラに伝えます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-559">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="2c1a8-560">戻り値の型 `Task<IActionResult>` は、進行中の作業と型 `IActionResult` の結果を表します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-560">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="2c1a8-561">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-561">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="2c1a8-562">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-562">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="2c1a8-563">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-563">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="2c1a8-564">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-564">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="2c1a8-565">Entity Framework を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="2c1a8-565">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="2c1a8-566">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-566">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="2c1a8-567">たとえば、`ToListAsync`、`SingleOrDefaultAsync`、`SaveChangesAsync` などです。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-567">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="2c1a8-568">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-568">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="2c1a8-569">EF コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-569">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="2c1a8-570">非同期 EF メソッドを呼び出すとき、`await` キーワードを常に使用します。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-570">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="2c1a8-571">非同期コードのパフォーマンス上の利点を最大限に活用する場合、(ページングなどのために) ライブラリ パッケージを利用しているのであれば、それがクエリをデータベースに送信させる Entity Framework メソッドを呼び出す場合、非同期を利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-571">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="2c1a8-572">.NET の非同期プログラミングについては、「[非同期の概要](/dotnet/articles/standard/async)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-572">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2c1a8-573">次のステップ</span><span class="sxs-lookup"><span data-stu-id="2c1a8-573">Next steps</span></span>

<span data-ttu-id="2c1a8-574">基本的な CRUD (作成、読み取り、更新、削除) 操作の実行方法を学習するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="2c1a8-574">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2c1a8-575">基本 CRUD 機能を実装する</span><span class="sxs-lookup"><span data-stu-id="2c1a8-575">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
