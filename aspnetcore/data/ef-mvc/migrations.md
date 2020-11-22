---
title: チュートリアル パート 5 - Contoso University のサンプルに移行を適用する
description: Contoso University チュートリアル シリーズのパート 5。 ASP.NET Core MVC アプリでのデータ モデルの変更を管理するために EF Core の移行機能を使用します。
author: rick-anderson
ms.author: riande
ms.custom: contperfq2
ms.date: 11/13/2020
ms.topic: tutorial
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: ab5be222416e61fcff90c5130ca91ad4a2a5c9b0
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674005"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="9b83f-104">チュートリアル: パート 5 - Contoso University のサンプルに移行を適用する</span><span class="sxs-lookup"><span data-stu-id="9b83f-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="9b83f-105">このチュートリアルでは、データ モデルの変更を管理するための EF Core の移行機能の使用を開始します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="9b83f-106">チュートリアルの後半では、データ モデルを変更するときに移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="9b83f-107">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="9b83f-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9b83f-108">移行について学習する</span><span class="sxs-lookup"><span data-stu-id="9b83f-108">Learn about migrations</span></span>
> * <span data-ttu-id="9b83f-109">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="9b83f-109">Create an initial migration</span></span>
> * <span data-ttu-id="9b83f-110">Up および Down メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="9b83f-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="9b83f-111">データ モデルのスナップショットについて学習する</span><span class="sxs-lookup"><span data-stu-id="9b83f-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="9b83f-112">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="9b83f-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9b83f-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9b83f-113">Prerequisites</span></span>

* [<span data-ttu-id="9b83f-114">並べ替え、フィルター処理、ページング</span><span class="sxs-lookup"><span data-stu-id="9b83f-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="9b83f-115">移行について</span><span class="sxs-lookup"><span data-stu-id="9b83f-115">About migrations</span></span>

<span data-ttu-id="9b83f-116">新しいアプリケーションを開発して、データ モデルが頻繁に変更される場合、モデルが変更されるたびに、モデルはデータベースと同期されなくなります。</span><span class="sxs-lookup"><span data-stu-id="9b83f-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="9b83f-117">データベースが存在しない場合にデータベースを作成するように Entity Framework を構成して、これらのチュートリアルを開始しました。</span><span class="sxs-lookup"><span data-stu-id="9b83f-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="9b83f-118">そのため、データ モデルを変更する (エンティティ クラスを追加、削除、または変更するか、DbContext クラスを変更する) たびに、データベースを削除することができ、EF でモデルに一致する新しいデータベースを作成し、テスト データを使ってシードを設定します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="9b83f-119">このメソッドは、実稼働環境にアプリケーションを展開するまで、データベースとデータ モデルの同期の維持がうまく機能します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="9b83f-120">実稼働環境でアプリケーションを実行している場合、通常、保持する必要があるデータが保存され、新しい列の追加などの変更を加えるたびにすべてが失われないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b83f-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="9b83f-121">EF Core の移行機能は、新しいデータベースを作成する代わりに、EF でデータベース スキーマを更新できるようにすることで、この問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="9b83f-122">移行の作業を行うには、**パッケージ マネージャー コンソール** (PMC) または CLI を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="9b83f-123">このチュートリアルでは、CLI コマンドを使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="9b83f-124">PMC については、[このチュートリアルの最後](#pmc)に説明します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="9b83f-125">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-125">Drop the database</span></span>

<span data-ttu-id="9b83f-126">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-126">Delete the database.</span></span> <span data-ttu-id="9b83f-127">**SQL Server オブジェクト エクスプローラー** (SSOX) または `database drop` CLI コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-127">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

<span data-ttu-id="9b83f-128">次のセクションでは、CLI コマンドを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-128">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="9b83f-129">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="9b83f-129">Create an initial migration</span></span>

<span data-ttu-id="9b83f-130">変更を保存し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="9b83f-130">Save your changes and build the project.</span></span> <span data-ttu-id="9b83f-131">次に、コマンド ウィンドウを開き、プロジェクト フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-131">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="9b83f-132">この操作を行う簡単な方法を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-132">Here's a quick way to do that:</span></span>

* <span data-ttu-id="9b83f-133">**[ソリューション エクスプローラー]** で、プロジェクトを右クリックし、コンテキスト メニューの **[エクスプローラーでフォルダーを開く]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-133">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![[エクスプローラーで開く] メニュー項目](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="9b83f-135">アドレス バーに「cmd」と入力して、Enter キーを押します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-135">Enter "cmd" in the address bar and press Enter.</span></span>

  ![コマンド ウィンドウを開く](migrations/_static/open-command-window.png)

<span data-ttu-id="9b83f-137">コマンド ウィンドウで次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-137">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="9b83f-138">`dotnet tool install --global dotnet-ef` によって、[グローバル ツール](/ef/core/miscellaneous/cli/dotnet)として `dotnet ef` がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-138">`dotnet tool install --global dotnet-ef` installs `dotnet ef` as a [global tool](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="9b83f-139">上のコマンドでは、次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-139">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="9b83f-140">"*別のプロセスによって使用されているため、ContosoUniversity.dll ファイルにアクセスできません。* " というエラー メッセージが表示された場合は、Windows のシステム トレイの IIS Express アイコンを見つけて右クリックし、 **[ContosoUniversity]、[サイトの停止]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="9b83f-140">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="9b83f-141">Up および Down メソッドを確認する</span><span class="sxs-lookup"><span data-stu-id="9b83f-141">Examine Up and Down methods</span></span>

<span data-ttu-id="9b83f-142">`migrations add` コマンドを実行したときに、EF では最初からデータベースを作成するコードが生成されています。</span><span class="sxs-lookup"><span data-stu-id="9b83f-142">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="9b83f-143">このコードは、 *\<timestamp>_InitialCreate.cs* という名前のファイルにある *[Migrations]* フォルダー内にあります。</span><span class="sxs-lookup"><span data-stu-id="9b83f-143">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="9b83f-144">`InitialCreate` クラスの `Up` メソッドでは、データ モデルのエンティティ セットに対応するデータベース テーブルを作成し、`Down` メソッドでは、次の例に示すようにそれらを削除します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-144">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="9b83f-145">移行は、`Up` メソッドを呼び出して、移行のためのデータ モデルの変更を実装します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-145">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="9b83f-146">更新をロールバックするためのコマンドを入力すると、移行が `Down` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-146">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="9b83f-147">このコードは、`migrations add InitialCreate` コマンドを入力したときに作成された初期移行向けのものです。</span><span class="sxs-lookup"><span data-stu-id="9b83f-147">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="9b83f-148">移行の name パラメーター (この例では "InitialCreate") は、ファイル名に使用され、どのような名前でも付けることができます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-148">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="9b83f-149">移行で行われている処理をまとめた単語または語句を選択することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9b83f-149">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="9b83f-150">たとえば、後で移行に "AddDepartmentTable" という名前を付ける可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9b83f-150">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="9b83f-151">データベースが既に存在するときに、初期移行を作成した場合、データベースの作成コードが生成されますが、データベースは既にデータと一致しているため、作成コードを実行する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9b83f-151">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="9b83f-152">データベースがまだ存在しない別の環境にアプリを展開する場合、データベースを作成するために、このコードが実行されるため、最初にテストを行うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9b83f-152">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="9b83f-153">これが、移行で新しいデータベースを最初から作成できるように、前の接続文字列でデータベースの名前を変更した理由です。</span><span class="sxs-lookup"><span data-stu-id="9b83f-153">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="9b83f-154">データ モデルのスナップショット</span><span class="sxs-lookup"><span data-stu-id="9b83f-154">The data model snapshot</span></span>

<span data-ttu-id="9b83f-155">移行は、現在のデータベース スキーマの *スナップショット* を *Migrations/SchoolContextModelSnapshot.cs* 内に作成します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-155">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="9b83f-156">移行を追加するときに、EF は、スナップショット ファイルとデータ モデルを比較することによって変更内容を判断します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-156">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="9b83f-157">移行を削除するには、[dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-157">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="9b83f-158">`dotnet ef migrations remove` によって移行が削除され、スナップショットが正しくリセットされたことが確認されます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-158">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="9b83f-159">`dotnet ef migrations remove` が失敗した場合は、`dotnet ef migrations remove -v` を使用して、エラーに関する詳細情報を確認してください。</span><span class="sxs-lookup"><span data-stu-id="9b83f-159">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="9b83f-160">スナップショット ファイルの使用方法の詳細については、[チーム環境での EF Core 移行](/ef/core/managing-schemas/migrations/teams)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b83f-160">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="9b83f-161">移行を適用する</span><span class="sxs-lookup"><span data-stu-id="9b83f-161">Apply the migration</span></span>

<span data-ttu-id="9b83f-162">コマンド ウィンドウで、次のコマンドを入力してデータベースとデータベース内のテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-162">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="9b83f-163">コマンドからの出力は、データベースを設定する SQL コマンドのログを表示する以外は、`migrations add` コマンドと同様です。</span><span class="sxs-lookup"><span data-stu-id="9b83f-163">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="9b83f-164">次のサンプル出力では、ログのほとんどは省略されています。</span><span class="sxs-lookup"><span data-stu-id="9b83f-164">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="9b83f-165">ログ メッセージの詳細レベルを表示しない場合は、*appsettings.Development.json* ファイルでログ レベルを変更できます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-165">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="9b83f-166">詳細については、「<xref:fundamentals/logging/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b83f-166">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

<span data-ttu-id="9b83f-167">**SQL Server オブジェクト エクスプローラー** を使用して、最初のチュートリアルで行ったように、データベースを調べます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-167">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="9b83f-168">データベースに適用されている移行を記録する \_\_EFMigrationsHistory テーブルに追加があることがわかります。</span><span class="sxs-lookup"><span data-stu-id="9b83f-168">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="9b83f-169">テーブルのデータを表示すると、最初の移行の 1 行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-169">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="9b83f-170">(前の CLI の出力例の最後のログは、この行を作成する INSERT ステートメントを示しています)。</span><span class="sxs-lookup"><span data-stu-id="9b83f-170">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="9b83f-171">アプリケーションを実行して、すべてが以前と同じように動作することを確認します。</span><span class="sxs-lookup"><span data-stu-id="9b83f-171">Run the application to verify that everything still works the same as before.</span></span>

![Students インデックス ページ](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="9b83f-173">CLI と PMC を比較する</span><span class="sxs-lookup"><span data-stu-id="9b83f-173">Compare CLI and PMC</span></span>

<span data-ttu-id="9b83f-174">移行を管理するための EF ツールは、.NET Core CLI コマンドから、または Visual Studio **パッケージ マネージャー コンソール** (PMC) ウィンドウの PowerShell コマンドレットから利用できます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-174">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="9b83f-175">このチュートリアルでは、CLI の使用方法を示しますが、好みに応じて PMC を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b83f-175">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="9b83f-176">PMC コマンドの EF コマンドは、[Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) パッケージ内にあります。</span><span class="sxs-lookup"><span data-stu-id="9b83f-176">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="9b83f-177">このパッケージは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれているので、アプリに `Microsoft.AspNetCore.App` のパッケージ参照がある場合、パッケージ参照を追加する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9b83f-177">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="9b83f-178">**重要:** これは、 *.csproj* ファイルを編集して CLI 用にインストールするものと同じパッケージではありません。</span><span class="sxs-lookup"><span data-stu-id="9b83f-178">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="9b83f-179">`Tools.DotNet` で終わる CLI パッケージ名とは異なり、このパッケージの名前は `Tools` で終わります。</span><span class="sxs-lookup"><span data-stu-id="9b83f-179">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="9b83f-180">CLI コマンドの詳細については、「[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b83f-180">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="9b83f-181">PMC コマンドの詳細については、「[パッケージ マネージャー コンソール (Visual Studio)](/ef/core/miscellaneous/cli/powershell)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b83f-181">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="9b83f-182">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="9b83f-182">Get the code</span></span>

[<span data-ttu-id="9b83f-183">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="9b83f-183">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="9b83f-184">次のステップ</span><span class="sxs-lookup"><span data-stu-id="9b83f-184">Next step</span></span>

<span data-ttu-id="9b83f-185">データ モデルの展開に関するより高度なトピックを確認するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="9b83f-185">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="9b83f-186">その途中で、追加の移行を作成して適用することになります。</span><span class="sxs-lookup"><span data-stu-id="9b83f-186">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9b83f-187">追加の移行を作成して適用する</span><span class="sxs-lookup"><span data-stu-id="9b83f-187">Create and apply additional migrations</span></span>](complex-data-model.md)
