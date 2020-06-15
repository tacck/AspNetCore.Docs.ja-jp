---
title: ASP.NET Core と MongoDB で Web API を作成する
author: prkhandelwal
description: このチュートリアルは、MongoDB NoSQL データベースを使用して ASP.NET Core Web API を作成する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mongo-app
ms.openlocfilehash: 46607fc92670bb46a155ddf3248bc8a36b600a4a
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452253"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="3be06-103">ASP.NET Core と MongoDB で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="3be06-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="3be06-104">作成者: [Pratik Khandelwal](https://twitter.com/K2Prk) および [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="3be06-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3be06-105">このチュートリアルでは、[MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL データベース上で Create、Read、Update、Delete の各操作 (CRUD) を実行するWeb API を作成します。</span><span class="sxs-lookup"><span data-stu-id="3be06-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="3be06-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3be06-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3be06-107">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="3be06-107">Configure MongoDB</span></span>
> * <span data-ttu-id="3be06-108">MongoDB データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="3be06-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="3be06-109">MongoDB のコレクションとスキーマを定義する</span><span class="sxs-lookup"><span data-stu-id="3be06-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="3be06-110">Web API から MongoDB CRUD 操作を実行する</span><span class="sxs-lookup"><span data-stu-id="3be06-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="3be06-111">JSON のシリアル化のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="3be06-111">Customize JSON serialization</span></span>

<span data-ttu-id="3be06-112">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3be06-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3be06-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3be06-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3be06-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3be06-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="3be06-115">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="3be06-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="3be06-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="3be06-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="3be06-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3be06-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3be06-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3be06-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="3be06-119">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="3be06-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="3be06-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3be06-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="3be06-121">Visual Studio Code 用 C#</span><span class="sxs-lookup"><span data-stu-id="3be06-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="3be06-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3be06-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3be06-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3be06-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3be06-124">.NET Core SDK 3.0 以降</span><span class="sxs-lookup"><span data-stu-id="3be06-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="3be06-125">Visual Studio for Mac バージョン 7.7 以降</span><span class="sxs-lookup"><span data-stu-id="3be06-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="3be06-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3be06-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="3be06-127">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="3be06-127">Configure MongoDB</span></span>

<span data-ttu-id="3be06-128">Windows を使用する場合、MongoDB は既定では *C:\\Program Files\\MongoDB* にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="3be06-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="3be06-129">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* を `Path` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="3be06-130">この変更により、開発用コンピューターのどこからでも MongoDB にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="3be06-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="3be06-131">次の手順では mongo シェルを使用して、データベースを作成し、コレクションを作成し、ドキュメントを保存します。</span><span class="sxs-lookup"><span data-stu-id="3be06-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="3be06-132">mongo のシェル コマンドについて詳しくは、「[Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)」(mongo シェルの使用) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3be06-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="3be06-133">データを格納するために開発用コンピューター上のディレクトリを選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="3be06-134">たとえば、Windows では *C:\\BooksData* です。</span><span class="sxs-lookup"><span data-stu-id="3be06-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="3be06-135">存在しない場合はディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="3be06-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="3be06-136">mongo シェルでは新しいディレクトリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="3be06-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="3be06-137">コマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="3be06-137">Open a command shell.</span></span> <span data-ttu-id="3be06-138">次のコマンドを実行して、既定のポート 27017 で MongoDB に接続します。</span><span class="sxs-lookup"><span data-stu-id="3be06-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="3be06-139">忘れずに、前の手順で選択したディレクトリで `<data_directory_path>` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3be06-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="3be06-140">別のコマンド シェル インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="3be06-140">Open another command shell instance.</span></span> <span data-ttu-id="3be06-141">次のコマンドを実行して、既定のテスト データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="3be06-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="3be06-142">コマンド シェルで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="3be06-143">これがまだ存在していない場合、*BookstoreDb* という名前のデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="3be06-144">データベースが存在する場合は、トランザクションのために接続されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="3be06-145">次のコマンドを使用して `Books` コレクションを作成します。</span><span class="sxs-lookup"><span data-stu-id="3be06-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="3be06-146">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="3be06-147">次のコマンドを使用して、`Books` コレクションのスキーマを定義し、2 つのドキュメントを挿入します。</span><span class="sxs-lookup"><span data-stu-id="3be06-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="3be06-148">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="3be06-149">この記事に示されている ID は、このサンプルを実行するときの ID とは一致していません。</span><span class="sxs-lookup"><span data-stu-id="3be06-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="3be06-150">次のコマンドを使用して、データベース内のドキュメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="3be06-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="3be06-151">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="3be06-152">スキーマによって、自動生成された型が `ObjectId` の `_id` プロパティが各ドキュメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="3be06-153">データベースの準備ができました。</span><span class="sxs-lookup"><span data-stu-id="3be06-153">The database is ready.</span></span> <span data-ttu-id="3be06-154">ASP.NET Core Web API の作成を開始できます。</span><span class="sxs-lookup"><span data-stu-id="3be06-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="3be06-155">ASP.NET Core Web API プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="3be06-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3be06-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3be06-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3be06-157">**[ファイル]** > **[新規作成]** > **[プロジェクト]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="3be06-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="3be06-158">**[ASP.NET Core Web アプリケーション]** プロジェクトの種類を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="3be06-159">プロジェクトに *BooksApi* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="3be06-160">**[.NET Core]** ターゲット フレームワークと **[ASP.NET Core 3.0]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="3be06-161">**[API]** プロジェクト テンプレートを選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="3be06-162">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="3be06-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3be06-163">**[パッケージ マネージャー コンソール]** ウィンドウで、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="3be06-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="3be06-164">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="3be06-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3be06-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3be06-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3be06-166">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="3be06-167">.NET Core をターゲットとする新しい ASP.NET Core Web API プロジェクトが生成され、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="3be06-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="3be06-168">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'BooksApi' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="3be06-169">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-169">Select **Yes**.</span></span>
1. <span data-ttu-id="3be06-170">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="3be06-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3be06-171">**[統合ターミナル]** を開き、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="3be06-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="3be06-172">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="3be06-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3be06-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3be06-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3be06-174">バージョン 8.6 より前の Visual Studio for Mac の場合、サイドバーから **[ファイル]** 、 **[新しいソリューション]** 、 **[.NET Core]** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="3be06-175">バージョン 8.6 以降の場合、サイドバーから **[ファイル]** 、 **[新しいソリューション]** 、 **[Web and Console]\(Web とコンソール)** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="3be06-176">**[ASP.NET Core]** 、 **[API]** C# プロジェクト テンプレートの順に選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="3be06-177">**[ターゲット フレームワーク]** ドロップダウン リストで **[.NET Core 3.1]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="3be06-178">**[プロジェクト名]** に「*BooksApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-178">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="3be06-179">**[ソリューション]** パッドで、プロジェクトの **[依存関係]** ノードを右クリックし、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="3be06-180">検索ボックスに「*MongoDB.Driver*」と入力し、*MongoDB.Driver* パッケージを選択して、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="3be06-181">**[ライセンスへの同意]** ダイアログの **[承諾]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="3be06-182">モデルにエンティティを追加する</span><span class="sxs-lookup"><span data-stu-id="3be06-182">Add an entity model</span></span>

1. <span data-ttu-id="3be06-183">*Models* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="3be06-184">次のコードを使用して、`Book` クラスを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="3be06-185">上記のクラスでは、`Id` プロパティは:</span><span class="sxs-lookup"><span data-stu-id="3be06-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="3be06-186">共通言語ランタイム (CLR) オブジェクトを MongoDB コレクションにマッピングするために必須です。</span><span class="sxs-lookup"><span data-stu-id="3be06-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="3be06-187">ドキュメントの主キーとしてこのプロパティを指定するために、[`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="3be06-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="3be06-188">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 構造体ではなく `string` 型としてパラメーターを渡すことができるようにするために、[`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="3be06-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="3be06-189">Mongo によって `string` から `ObjectId` への変換が処理されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="3be06-190">`BookName` プロパティには、[`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 属性を使用して注釈が付けられます。</span><span class="sxs-lookup"><span data-stu-id="3be06-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="3be06-191">属性の値 `Name` は、MongoDB コレクションでのプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="3be06-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="3be06-192">構成モデルを追加する</span><span class="sxs-lookup"><span data-stu-id="3be06-192">Add a configuration model</span></span>

1. <span data-ttu-id="3be06-193">次のデータベース構成値を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-193">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="3be06-194">次のコードを使用して、*BookstoreDatabaseSettings.cs* ファイルを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="3be06-195">前述の `BookstoreDatabaseSettings` クラスは、*appsettings.json* ファイルの `BookstoreDatabaseSettings` プロパティ値を格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="3be06-196">JSON と C# のプロパティ名には、マッピング処理を簡単にするために同じ名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="3be06-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="3be06-197">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="3be06-198">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3be06-198">In the preceding code:</span></span>

   * <span data-ttu-id="3be06-199">*appsettings.json* ファイルの `BookstoreDatabaseSettings` セクションがバインドされている構成インスタンスは、Dependency Injection (DI) コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="3be06-200">たとえば、`BookstoreDatabaseSettings` オブジェクトの `ConnectionString` プロパティには、*appsettings.json* の `BookstoreDatabaseSettings:ConnectionString` プロパティが設定されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="3be06-201">`IBookstoreDatabaseSettings` インターフェイスは、シングルトン [サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3be06-202">挿入されると、インターフェイス インスタンスは `BookstoreDatabaseSettings` オブジェクトに解決されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="3be06-203">次のコードを *Startup.cs* の先頭に追加して、`BookstoreDatabaseSettings` と `IBookstoreDatabaseSettings` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="3be06-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="3be06-204">CRUD 操作のサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="3be06-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="3be06-205">*Services* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="3be06-206">次のコードを使用して、`BookService` クラスを *Services* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="3be06-207">前述のコードでは、`IBookstoreDatabaseSettings` インスタンスがコンストラクターの挿入によって DI から取得されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="3be06-208">この手法で、「[構成モデルを追加する](#add-a-configuration-model)」セクションで追加した *appsettings.json* 構成値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3be06-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="3be06-209">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="3be06-210">前述のコードでは、消費クラスへのコンストラクターの挿入をサポートする `BookService` クラスが DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="3be06-211">`BookService` が `MongoClient` に直接依存しているため、シングルトン サービスの有効期間が最も適切です。</span><span class="sxs-lookup"><span data-stu-id="3be06-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="3be06-212">公式の [Mongo Client 再利用ガイドライン](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)に従い、シングルトン サービスの有効期間を使用して DI に `MongoClient` を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3be06-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="3be06-213">次のコードを *Startup.cs* の先頭に追加して、`BookService` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="3be06-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="3be06-214">`BookService` クラスは次の `MongoDB.Driver` メンバーを使用して、データベースに対する CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="3be06-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm):データベース操作を実行するためにサーバー インスタンスを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="3be06-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="3be06-216">このクラスのコンストラクターには MongoDB 接続文字列が提供されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="3be06-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm):操作を実行する Mongo データベースを表します。</span><span class="sxs-lookup"><span data-stu-id="3be06-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="3be06-218">このチュートリアルでは、インターフェイスで汎用の [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) メソッドを使用し、特定のコレクション内のデータへのアクセスを取得します。</span><span class="sxs-lookup"><span data-stu-id="3be06-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="3be06-219">このメソッドが呼び出された後に、CRUD 操作をこのコレクションに対して実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="3be06-220">`GetCollection<TDocument>(collection)` メソッドの呼び出しの内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3be06-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="3be06-221">`collection` はコレクション名です。</span><span class="sxs-lookup"><span data-stu-id="3be06-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="3be06-222">`TDocument` はコレクションに格納されている CLR オブジェクト型です。</span><span class="sxs-lookup"><span data-stu-id="3be06-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="3be06-223">`GetCollection<TDocument>(collection)` から、コレクションを表す [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="3be06-224">このチュートリアルでは、コレクションに対して次のメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="3be06-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="3be06-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm):指定された検索基準と一致する 1 つのドキュメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="3be06-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="3be06-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm):指定された検索基準と一致するコレクション内のすべてのドキュメントを返します。</span><span class="sxs-lookup"><span data-stu-id="3be06-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="3be06-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm):指定されたオブジェクトを新しいドキュメントとしてコレクションに挿入します。</span><span class="sxs-lookup"><span data-stu-id="3be06-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="3be06-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm):指定された検索基準と一致する 1 つのドキュメントを、指定されたオブジェクトで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3be06-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3be06-229">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="3be06-229">Add a controller</span></span>

<span data-ttu-id="3be06-230">次のコードを使用して、`BooksController` クラスを *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="3be06-231">上記の Web API コントローラー:</span><span class="sxs-lookup"><span data-stu-id="3be06-231">The preceding web API controller:</span></span>

* <span data-ttu-id="3be06-232">`BookService` クラスを使用して CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="3be06-233">GET、POST、PUT、DELETE HTTP 要求をサポートするアクション メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3be06-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="3be06-234">`Create` アクション メソッドで <xref:System.Web.Http.ApiController.CreatedAtRoute*> を呼び出して、[HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="3be06-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="3be06-235">状態コード 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="3be06-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="3be06-236">`CreatedAtRoute` によって、応答に `Location` ヘッダーも追加されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="3be06-237">`Location` ヘッダーでは、新しく作成されたブックの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="3be06-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="3be06-238">Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="3be06-238">Test the web API</span></span>

1. <span data-ttu-id="3be06-239">アプリケーションをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-239">Build and run the app.</span></span>

1. <span data-ttu-id="3be06-240">`http://localhost:<port>/api/books` に移動して、コントローラーのパラメーターなしの `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="3be06-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="3be06-241">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="3be06-242">`http://localhost:<port>/api/books/{id here}` に移動して、コントローラーのオーバーロードされた `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="3be06-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="3be06-243">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="3be06-244">JSON シリアル化オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="3be06-244">Configure JSON serialization options</span></span>

<span data-ttu-id="3be06-245">「[Web API をテストする](#test-the-web-api)」セクションで返される JSON 応答について変更すべき 2 つの詳細があります。</span><span class="sxs-lookup"><span data-stu-id="3be06-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="3be06-246">プロパティ名の既定の camel 形式は、CLR オブジェクトのプロパティ名の Pascal 形式と一致するように変更する必要があります</span><span class="sxs-lookup"><span data-stu-id="3be06-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="3be06-247">`bookName` プロパティは `Name` として返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="3be06-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="3be06-248">上記の要件を満たすには、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="3be06-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="3be06-249">JSON.NET は ASP.NET 共有フレームワークから削除されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="3be06-250">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) へのパッケージ参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="3be06-251">`Startup.ConfigureServices` で、次の強調表示されたコードを `AddControllers` メソッド呼び出しにチェーンします。</span><span class="sxs-lookup"><span data-stu-id="3be06-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="3be06-252">上記の変更により、Web API のシリアル化された JSON 応答内のプロパティ名は、CLR のオブジェクトの種類での対応するプロパティ名と一致しています。</span><span class="sxs-lookup"><span data-stu-id="3be06-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="3be06-253">たとえば、`Book` クラスの `Author` プロパティは `Author` としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="3be06-254">*Models/Book.cs* では、`BookName` プロパティに次の [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 属性を使用して注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="3be06-254">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="3be06-255">`[JsonProperty]`属性の値 `Name` は、Web API のシリアル化された JSON 応答内のプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="3be06-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="3be06-256">次のコードを *Models/Book.cs* の先頭に追加して、`[JsonProperty]` 属性の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="3be06-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="3be06-257">「[Web API をテストする](#test-the-web-api)」セクションで定義されている手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="3be06-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="3be06-258">JSON プロパティ名の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3be06-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3be06-259">このチュートリアルでは、[MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL データベース上で Create、Read、Update、Delete の各操作 (CRUD) を実行するWeb API を作成します。</span><span class="sxs-lookup"><span data-stu-id="3be06-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="3be06-260">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3be06-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3be06-261">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="3be06-261">Configure MongoDB</span></span>
> * <span data-ttu-id="3be06-262">MongoDB データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="3be06-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="3be06-263">MongoDB のコレクションとスキーマを定義する</span><span class="sxs-lookup"><span data-stu-id="3be06-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="3be06-264">Web API から MongoDB CRUD 操作を実行する</span><span class="sxs-lookup"><span data-stu-id="3be06-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="3be06-265">JSON のシリアル化のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="3be06-265">Customize JSON serialization</span></span>

<span data-ttu-id="3be06-266">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="3be06-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3be06-267">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="3be06-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3be06-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3be06-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="3be06-269">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="3be06-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="3be06-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="3be06-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="3be06-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3be06-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3be06-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3be06-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="3be06-273">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="3be06-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="3be06-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3be06-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="3be06-275">Visual Studio Code 用 C#</span><span class="sxs-lookup"><span data-stu-id="3be06-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="3be06-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3be06-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3be06-277">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3be06-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="3be06-278">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="3be06-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="3be06-279">Visual Studio for Mac バージョン 7.7 以降</span><span class="sxs-lookup"><span data-stu-id="3be06-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="3be06-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="3be06-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="3be06-281">MongoDB を構成する</span><span class="sxs-lookup"><span data-stu-id="3be06-281">Configure MongoDB</span></span>

<span data-ttu-id="3be06-282">Windows を使用する場合、MongoDB は既定では *C:\\Program Files\\MongoDB* にインストールされます。</span><span class="sxs-lookup"><span data-stu-id="3be06-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="3be06-283">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* を `Path` 環境変数に追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="3be06-284">この変更により、開発用コンピューターのどこからでも MongoDB にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="3be06-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="3be06-285">次の手順では mongo シェルを使用して、データベースを作成し、コレクションを作成し、ドキュメントを保存します。</span><span class="sxs-lookup"><span data-stu-id="3be06-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="3be06-286">mongo のシェル コマンドについて詳しくは、「[Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)」(mongo シェルの使用) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3be06-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="3be06-287">データを格納するために開発用コンピューター上のディレクトリを選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="3be06-288">たとえば、Windows では *C:\\BooksData* です。</span><span class="sxs-lookup"><span data-stu-id="3be06-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="3be06-289">存在しない場合はディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="3be06-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="3be06-290">mongo シェルでは新しいディレクトリは作成されません。</span><span class="sxs-lookup"><span data-stu-id="3be06-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="3be06-291">コマンド シェルを開きます。</span><span class="sxs-lookup"><span data-stu-id="3be06-291">Open a command shell.</span></span> <span data-ttu-id="3be06-292">次のコマンドを実行して、既定のポート 27017 で MongoDB に接続します。</span><span class="sxs-lookup"><span data-stu-id="3be06-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="3be06-293">忘れずに、前の手順で選択したディレクトリで `<data_directory_path>` を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3be06-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="3be06-294">別のコマンド シェル インスタンスを開きます。</span><span class="sxs-lookup"><span data-stu-id="3be06-294">Open another command shell instance.</span></span> <span data-ttu-id="3be06-295">次のコマンドを実行して、既定のテスト データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="3be06-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="3be06-296">コマンド シェルで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="3be06-297">これがまだ存在していない場合、*BookstoreDb* という名前のデータベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="3be06-298">データベースが存在する場合は、トランザクションのために接続されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="3be06-299">次のコマンドを使用して `Books` コレクションを作成します。</span><span class="sxs-lookup"><span data-stu-id="3be06-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="3be06-300">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="3be06-301">次のコマンドを使用して、`Books` コレクションのスキーマを定義し、2 つのドキュメントを挿入します。</span><span class="sxs-lookup"><span data-stu-id="3be06-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="3be06-302">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="3be06-303">この記事に示されている ID は、このサンプルを実行するときの ID とは一致していません。</span><span class="sxs-lookup"><span data-stu-id="3be06-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="3be06-304">次のコマンドを使用して、データベース内のドキュメントを表示します。</span><span class="sxs-lookup"><span data-stu-id="3be06-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="3be06-305">次のような結果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="3be06-306">スキーマによって、自動生成された型が `ObjectId` の `_id` プロパティが各ドキュメントに追加されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="3be06-307">データベースの準備ができました。</span><span class="sxs-lookup"><span data-stu-id="3be06-307">The database is ready.</span></span> <span data-ttu-id="3be06-308">ASP.NET Core Web API の作成を開始できます。</span><span class="sxs-lookup"><span data-stu-id="3be06-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="3be06-309">ASP.NET Core Web API プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="3be06-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3be06-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3be06-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3be06-311">**[ファイル]** > **[新規作成]** > **[プロジェクト]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="3be06-311">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="3be06-312">**[ASP.NET Core Web アプリケーション]** プロジェクトの種類を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-312">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="3be06-313">プロジェクトに *BooksApi* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-313">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="3be06-314">**[.NET Core]** ターゲット フレームワークと **[ASP.NET Core 2.2]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="3be06-315">**[API]** プロジェクト テンプレートを選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-315">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="3be06-316">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="3be06-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3be06-317">**[パッケージ マネージャー コンソール]** ウィンドウで、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="3be06-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="3be06-318">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="3be06-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3be06-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3be06-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="3be06-320">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="3be06-321">.NET Core をターゲットとする新しい ASP.NET Core Web API プロジェクトが生成され、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="3be06-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="3be06-322">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'BooksApi' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="3be06-323">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-323">Select **Yes**.</span></span>
1. <span data-ttu-id="3be06-324">[NuGet ギャラリー:MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) に関するページを参照して、MongoDB 用 .NET ドライバーの最新の安定バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="3be06-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="3be06-325">**[統合ターミナル]** を開き、プロジェクトのルートに移動します。</span><span class="sxs-lookup"><span data-stu-id="3be06-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="3be06-326">次のコマンドを実行して、MongoDB 用の .NET ドライバーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="3be06-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3be06-327">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3be06-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="3be06-328">バージョン 8.6 より前の Visual Studio for Mac の場合、サイドバーから **[ファイル]** 、 **[新しいソリューション]** 、 **[.NET Core]** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="3be06-329">バージョン 8.6 以降の場合、サイドバーから **[ファイル]** 、 **[新しいソリューション]** 、 **[Web and Console]\(Web とコンソール)** 、 **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="3be06-330">**[ASP.NET Core Web API]** C# プロジェクト テンプレートを選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-330">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="3be06-331">**[ターゲット フレームワーク]** ドロップダウン リストで **[.NET Core 2.2]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="3be06-332">**[プロジェクト名]** に「*BooksApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-332">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="3be06-333">**[ソリューション]** パッドで、プロジェクトの **[依存関係]** ノードを右クリックし、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="3be06-334">検索ボックスに「*MongoDB.Driver*」と入力し、*MongoDB.Driver* パッケージを選択して、 **[パッケージを追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="3be06-335">**[ライセンスへの同意]** ダイアログの **[承諾]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3be06-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="3be06-336">モデルにエンティティを追加する</span><span class="sxs-lookup"><span data-stu-id="3be06-336">Add an entity model</span></span>

1. <span data-ttu-id="3be06-337">*Models* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="3be06-338">次のコードを使用して、`Book` クラスを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="3be06-339">上記のクラスでは、`Id` プロパティは:</span><span class="sxs-lookup"><span data-stu-id="3be06-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="3be06-340">共通言語ランタイム (CLR) オブジェクトを MongoDB コレクションにマッピングするために必須です。</span><span class="sxs-lookup"><span data-stu-id="3be06-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="3be06-341">ドキュメントの主キーとしてこのプロパティを指定するために、[`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="3be06-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="3be06-342">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 構造体ではなく `string` 型としてパラメーターを渡すことができるようにするために、[`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) で注釈を付けられています。</span><span class="sxs-lookup"><span data-stu-id="3be06-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="3be06-343">Mongo によって `string` から `ObjectId` への変換が処理されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="3be06-344">`BookName` プロパティには、[`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 属性を使用して注釈が付けられます。</span><span class="sxs-lookup"><span data-stu-id="3be06-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="3be06-345">属性の値 `Name` は、MongoDB コレクションでのプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="3be06-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="3be06-346">構成モデルを追加する</span><span class="sxs-lookup"><span data-stu-id="3be06-346">Add a configuration model</span></span>

1. <span data-ttu-id="3be06-347">次のデータベース構成値を *appsettings.json* に追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-347">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="3be06-348">次のコードを使用して、*BookstoreDatabaseSettings.cs* ファイルを *Models* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="3be06-349">前述の `BookstoreDatabaseSettings` クラスは、*appsettings.json* ファイルの `BookstoreDatabaseSettings` プロパティ値を格納するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="3be06-350">JSON と C# のプロパティ名には、マッピング処理を簡単にするために同じ名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="3be06-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="3be06-351">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="3be06-352">上のコードでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="3be06-352">In the preceding code:</span></span>

   * <span data-ttu-id="3be06-353">*appsettings.json* ファイルの `BookstoreDatabaseSettings` セクションがバインドされている構成インスタンスは、Dependency Injection (DI) コンテナーに登録されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="3be06-354">たとえば、`BookstoreDatabaseSettings` オブジェクトの `ConnectionString` プロパティには、*appsettings.json* の `BookstoreDatabaseSettings:ConnectionString` プロパティが設定されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="3be06-355">`IBookstoreDatabaseSettings` インターフェイスは、シングルトン [サービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)で DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="3be06-356">挿入されると、インターフェイス インスタンスは `BookstoreDatabaseSettings` オブジェクトに解決されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="3be06-357">次のコードを *Startup.cs* の先頭に追加して、`BookstoreDatabaseSettings` と `IBookstoreDatabaseSettings` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="3be06-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="3be06-358">CRUD 操作のサービスを追加する</span><span class="sxs-lookup"><span data-stu-id="3be06-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="3be06-359">*Services* ディレクトリをプロジェクトのルートに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="3be06-360">次のコードを使用して、`BookService` クラスを *Services* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="3be06-361">前述のコードでは、`IBookstoreDatabaseSettings` インスタンスがコンストラクターの挿入によって DI から取得されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="3be06-362">この手法で、「[構成モデルを追加する](#add-a-configuration-model)」セクションで追加した *appsettings.json* 構成値にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3be06-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="3be06-363">次の強調表示されたコードを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="3be06-364">前述のコードでは、消費クラスへのコンストラクターの挿入をサポートする `BookService` クラスが DI に登録されています。</span><span class="sxs-lookup"><span data-stu-id="3be06-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="3be06-365">`BookService` が `MongoClient` に直接依存しているため、シングルトン サービスの有効期間が最も適切です。</span><span class="sxs-lookup"><span data-stu-id="3be06-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="3be06-366">公式の [Mongo Client 再利用ガイドライン](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)に従い、シングルトン サービスの有効期間を使用して DI に `MongoClient` を登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3be06-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="3be06-367">次のコードを *Startup.cs* の先頭に追加して、`BookService` の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="3be06-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="3be06-368">`BookService` クラスは次の `MongoDB.Driver` メンバーを使用して、データベースに対する CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="3be06-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm):データベース操作を実行するためにサーバー インスタンスを読み取ります。</span><span class="sxs-lookup"><span data-stu-id="3be06-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="3be06-370">このクラスのコンストラクターには MongoDB 接続文字列が提供されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="3be06-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm):操作を実行する Mongo データベースを表します。</span><span class="sxs-lookup"><span data-stu-id="3be06-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="3be06-372">このチュートリアルでは、インターフェイスで汎用の [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) メソッドを使用し、特定のコレクション内のデータへのアクセスを取得します。</span><span class="sxs-lookup"><span data-stu-id="3be06-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="3be06-373">このメソッドが呼び出された後に、CRUD 操作をこのコレクションに対して実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="3be06-374">`GetCollection<TDocument>(collection)` メソッドの呼び出しの内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3be06-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="3be06-375">`collection` はコレクション名です。</span><span class="sxs-lookup"><span data-stu-id="3be06-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="3be06-376">`TDocument` はコレクションに格納されている CLR オブジェクト型です。</span><span class="sxs-lookup"><span data-stu-id="3be06-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="3be06-377">`GetCollection<TDocument>(collection)` から、コレクションを表す [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="3be06-378">このチュートリアルでは、コレクションに対して次のメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="3be06-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="3be06-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm):指定された検索基準と一致する 1 つのドキュメントを削除します。</span><span class="sxs-lookup"><span data-stu-id="3be06-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="3be06-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm):指定された検索基準と一致するコレクション内のすべてのドキュメントを返します。</span><span class="sxs-lookup"><span data-stu-id="3be06-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="3be06-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm):指定されたオブジェクトを新しいドキュメントとしてコレクションに挿入します。</span><span class="sxs-lookup"><span data-stu-id="3be06-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="3be06-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm):指定された検索基準と一致する 1 つのドキュメントを、指定されたオブジェクトで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3be06-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="3be06-383">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="3be06-383">Add a controller</span></span>

<span data-ttu-id="3be06-384">次のコードを使用して、`BooksController` クラスを *Controllers* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="3be06-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="3be06-385">上記の Web API コントローラー:</span><span class="sxs-lookup"><span data-stu-id="3be06-385">The preceding web API controller:</span></span>

* <span data-ttu-id="3be06-386">`BookService` クラスを使用して CRUD 操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="3be06-387">GET、POST、PUT、DELETE HTTP 要求をサポートするアクション メソッドが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3be06-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="3be06-388">`Create` アクション メソッドで <xref:System.Web.Http.ApiController.CreatedAtRoute*> を呼び出して、[HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 応答を返します。</span><span class="sxs-lookup"><span data-stu-id="3be06-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="3be06-389">状態コード 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="3be06-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="3be06-390">`CreatedAtRoute` によって、応答に `Location` ヘッダーも追加されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="3be06-391">`Location` ヘッダーでは、新しく作成されたブックの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="3be06-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="3be06-392">Web API をテストする</span><span class="sxs-lookup"><span data-stu-id="3be06-392">Test the web API</span></span>

1. <span data-ttu-id="3be06-393">アプリケーションをビルドし、実行します。</span><span class="sxs-lookup"><span data-stu-id="3be06-393">Build and run the app.</span></span>

1. <span data-ttu-id="3be06-394">`http://localhost:<port>/api/books` に移動して、コントローラーのパラメーターなしの `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="3be06-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="3be06-395">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="3be06-396">`http://localhost:<port>/api/books/{id here}` に移動して、コントローラーのオーバーロードされた `Get` アクション メソッドをテストします。</span><span class="sxs-lookup"><span data-stu-id="3be06-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="3be06-397">次の JSON 応答が示されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="3be06-398">JSON シリアル化オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="3be06-398">Configure JSON serialization options</span></span>

<span data-ttu-id="3be06-399">「[Web API をテストする](#test-the-web-api)」セクションで返される JSON 応答について変更すべき 2 つの詳細があります。</span><span class="sxs-lookup"><span data-stu-id="3be06-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="3be06-400">プロパティ名の既定の camel 形式は、CLR オブジェクトのプロパティ名の Pascal 形式と一致するように変更する必要があります</span><span class="sxs-lookup"><span data-stu-id="3be06-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="3be06-401">`bookName` プロパティは `Name` として返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="3be06-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="3be06-402">上記の要件を満たすには、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="3be06-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="3be06-403">`Startup.ConfigureServices` で、次の強調表示されたコードを `AddMvc` メソッド呼び出しにチェーンします。</span><span class="sxs-lookup"><span data-stu-id="3be06-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="3be06-404">上記の変更により、Web API のシリアル化された JSON 応答内のプロパティ名は、CLR のオブジェクトの種類での対応するプロパティ名と一致しています。</span><span class="sxs-lookup"><span data-stu-id="3be06-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="3be06-405">たとえば、`Book` クラスの `Author` プロパティは `Author` としてシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="3be06-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="3be06-406">*Models/Book.cs* では、`BookName` プロパティに次の [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 属性を使用して注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="3be06-406">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="3be06-407">`[JsonProperty]`属性の値 `Name` は、Web API のシリアル化された JSON 応答内のプロパティ名を表します。</span><span class="sxs-lookup"><span data-stu-id="3be06-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="3be06-408">次のコードを *Models/Book.cs* の先頭に追加して、`[JsonProperty]` 属性の参照を解決します。</span><span class="sxs-lookup"><span data-stu-id="3be06-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="3be06-409">「[Web API をテストする](#test-the-web-api)」セクションで定義されている手順を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="3be06-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="3be06-410">JSON プロパティ名の違いに注意してください。</span><span class="sxs-lookup"><span data-stu-id="3be06-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="3be06-411">Web API に認証サポートを追加</span><span class="sxs-lookup"><span data-stu-id="3be06-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="3be06-412">次の手順</span><span class="sxs-lookup"><span data-stu-id="3be06-412">Next steps</span></span>

<span data-ttu-id="3be06-413">ASP.NET Core Web API の構築について詳しくは、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3be06-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="3be06-414">この記事の YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="3be06-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
