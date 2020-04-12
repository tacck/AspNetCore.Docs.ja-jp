---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
uid: tutorials/first-web-api
ms.openlocfilehash: 7418e962076fae3ebdbb25381838757b09046578
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417657"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="907a8-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="907a8-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="907a8-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5)、[Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="907a8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="907a8-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="907a8-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="907a8-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="907a8-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="907a8-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="907a8-107">Create a web API project.</span></span>
> * <span data-ttu-id="907a8-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="907a8-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="907a8-109">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="907a8-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="907a8-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="907a8-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="907a8-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="907a8-111">Call the web API with Postman.</span></span>

<span data-ttu-id="907a8-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="907a8-113">概要</span><span class="sxs-lookup"><span data-stu-id="907a8-113">Overview</span></span>

<span data-ttu-id="907a8-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="907a8-115">API</span><span class="sxs-lookup"><span data-stu-id="907a8-115">API</span></span> | <span data-ttu-id="907a8-116">説明</span><span class="sxs-lookup"><span data-stu-id="907a8-116">Description</span></span> | <span data-ttu-id="907a8-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="907a8-117">Request body</span></span> | <span data-ttu-id="907a8-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="907a8-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="907a8-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="907a8-119">GET /api/TodoItems</span></span> | <span data-ttu-id="907a8-120">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="907a8-120">Get all to-do items</span></span> | <span data-ttu-id="907a8-121">None</span><span class="sxs-lookup"><span data-stu-id="907a8-121">None</span></span> | <span data-ttu-id="907a8-122">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="907a8-122">Array of to-do items</span></span>|
|<span data-ttu-id="907a8-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="907a8-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="907a8-124">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="907a8-124">Get an item by ID</span></span> | <span data-ttu-id="907a8-125">None</span><span class="sxs-lookup"><span data-stu-id="907a8-125">None</span></span> | <span data-ttu-id="907a8-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="907a8-126">To-do item</span></span>|
|<span data-ttu-id="907a8-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="907a8-127">POST /api/TodoItems</span></span> | <span data-ttu-id="907a8-128">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-128">Add a new item</span></span> | <span data-ttu-id="907a8-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="907a8-129">To-do item</span></span> | <span data-ttu-id="907a8-130">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="907a8-130">To-do item</span></span> |
|<span data-ttu-id="907a8-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="907a8-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="907a8-132">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="907a8-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="907a8-133">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="907a8-133">To-do item</span></span> | <span data-ttu-id="907a8-134">None</span><span class="sxs-lookup"><span data-stu-id="907a8-134">None</span></span> |
|<span data-ttu-id="907a8-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="907a8-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="907a8-136">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="907a8-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="907a8-137">None</span><span class="sxs-lookup"><span data-stu-id="907a8-137">None</span></span> | <span data-ttu-id="907a8-138">None</span><span class="sxs-lookup"><span data-stu-id="907a8-138">None</span></span>|

<span data-ttu-id="907a8-139">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="907a8-139">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="907a8-145">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="907a8-145">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="907a8-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="907a8-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="907a8-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="907a8-149">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="907a8-149">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="907a8-151">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="907a8-152">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="907a8-153">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="907a8-154">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="907a8-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="907a8-155">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-155">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="907a8-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="907a8-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="907a8-158">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="907a8-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="907a8-159">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="907a8-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="907a8-160">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="907a8-161">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="907a8-162">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="907a8-162">The preceding commands:</span></span>

  * <span data-ttu-id="907a8-163">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="907a8-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="907a8-164">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="907a8-165">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="907a8-166">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-166">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="907a8-168">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="907a8-170">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.1]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="907a8-171">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="907a8-173">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="907a8-174">API のテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-174">Test the API</span></span>

<span data-ttu-id="907a8-175">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="907a8-176">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="907a8-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="907a8-178">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="907a8-179">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="907a8-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="907a8-180">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="907a8-181">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="907a8-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="907a8-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="907a8-183">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="907a8-184">ブラウザーで、次の URL に移動します: `https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="907a8-184">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="907a8-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="907a8-186">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="907a8-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="907a8-187">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="907a8-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="907a8-188">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="907a8-189">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="907a8-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="907a8-190">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="907a8-191">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-191">Add a model class</span></span>

<span data-ttu-id="907a8-192">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="907a8-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="907a8-193">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="907a8-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="907a8-195">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="907a8-196">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="907a8-197">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="907a8-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="907a8-198">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="907a8-199">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="907a8-200">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="907a8-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="907a8-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="907a8-202">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="907a8-203">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="907a8-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="907a8-205">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-205">Right-click the project.</span></span> <span data-ttu-id="907a8-206">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="907a8-207">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="907a8-207">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="907a8-209">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="907a8-210">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="907a8-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="907a8-212">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="907a8-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="907a8-213">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="907a8-214">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-214">Add a database context</span></span>

<span data-ttu-id="907a8-215">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="907a8-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="907a8-216">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="907a8-218">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="907a8-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="907a8-219">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="907a8-220">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="907a8-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="907a8-221">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="907a8-222">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="907a8-223">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="907a8-225">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="907a8-226">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="907a8-227">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="907a8-228">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="907a8-229">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="907a8-230">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="907a8-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="907a8-231">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="907a8-231">Register the database context</span></span>

<span data-ttu-id="907a8-232">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="907a8-233">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="907a8-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="907a8-234">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="907a8-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="907a8-235">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="907a8-235">The preceding code:</span></span>

* <span data-ttu-id="907a8-236">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="907a8-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="907a8-237">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="907a8-238">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="907a8-239">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="907a8-239">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="907a8-241">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="907a8-242">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="907a8-243">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="907a8-244">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="907a8-245">**モデル クラス**で **[TodoItem (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="907a8-246">**データ コンテキスト クラス**で **[TodoContext (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="907a8-247">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="907a8-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="907a8-248">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="907a8-249">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="907a8-250">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="907a8-250">The preceding commands:</span></span>

* <span data-ttu-id="907a8-251">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="907a8-252">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="907a8-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="907a8-253">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="907a8-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="907a8-254">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="907a8-254">The generated code:</span></span>

* <span data-ttu-id="907a8-255">クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="907a8-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="907a8-256">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="907a8-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="907a8-257">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="907a8-258">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="907a8-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="907a8-259">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="907a8-260">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="907a8-260">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="907a8-261">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="907a8-261">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="907a8-262">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="907a8-262">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="907a8-263">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-263">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="907a8-264">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="907a8-264">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="907a8-265">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="907a8-265">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="907a8-266">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-266">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="907a8-267">[`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="907a8-267">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="907a8-268">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="907a8-268">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="907a8-269"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="907a8-269">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="907a8-270">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-270">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="907a8-271">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="907a8-271">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="907a8-272">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-272">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="907a8-273">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-273">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="907a8-274">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-274">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="907a8-275">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-275">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="907a8-276">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="907a8-276">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="907a8-277">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="907a8-277">Install Postman</span></span>

<span data-ttu-id="907a8-278">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="907a8-278">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="907a8-279">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="907a8-279">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="907a8-280">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="907a8-280">Start the web app.</span></span>
* <span data-ttu-id="907a8-281">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="907a8-281">Start Postman.</span></span>
* <span data-ttu-id="907a8-282">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="907a8-282">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="907a8-283">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="907a8-283">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="907a8-284">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="907a8-284">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="907a8-285">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-285">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="907a8-286">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-286">Create a new request.</span></span>
* <span data-ttu-id="907a8-287">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-287">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="907a8-288">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-288">Select the **Body** tab.</span></span>
* <span data-ttu-id="907a8-289">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-289">Select the **raw** radio button.</span></span>
* <span data-ttu-id="907a8-290">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-290">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="907a8-291">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="907a8-291">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="907a8-292">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-292">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="907a8-294">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-294">Test the location header URI</span></span>

* <span data-ttu-id="907a8-295">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-295">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="907a8-296">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="907a8-296">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="907a8-298">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-298">Set the method to GET.</span></span>
* <span data-ttu-id="907a8-299">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="907a8-299">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="907a8-300">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-300">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="907a8-301">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="907a8-301">Examine the GET methods</span></span>

<span data-ttu-id="907a8-302">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="907a8-302">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="907a8-303">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="907a8-303">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="907a8-304">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="907a8-304">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="907a8-305">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-305">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="907a8-306">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="907a8-306">Test Get with Postman</span></span>

* <span data-ttu-id="907a8-307">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-307">Create a new request.</span></span>
* <span data-ttu-id="907a8-308">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-308">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="907a8-309">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-309">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="907a8-310">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="907a8-310">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="907a8-311">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-311">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="907a8-312">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-312">Select **Send**.</span></span>

<span data-ttu-id="907a8-313">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-313">This app uses an in-memory database.</span></span> <span data-ttu-id="907a8-314">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="907a8-314">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="907a8-315">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="907a8-315">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="907a8-316">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="907a8-316">Routing and URL paths</span></span>

<span data-ttu-id="907a8-317">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="907a8-317">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="907a8-318">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-318">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="907a8-319">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="907a8-319">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="907a8-320">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-320">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="907a8-321">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="907a8-321">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="907a8-322">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="907a8-322">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="907a8-323">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-323">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="907a8-324">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="907a8-324">This sample doesn't use a template.</span></span> <span data-ttu-id="907a8-325">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-325">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="907a8-326">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="907a8-326">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="907a8-327">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-327">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="907a8-328">戻り値</span><span class="sxs-lookup"><span data-stu-id="907a8-328">Return values</span></span>

<span data-ttu-id="907a8-329">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型です](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="907a8-329">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="907a8-330">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="907a8-330">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="907a8-331">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-331">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="907a8-332">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-332">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="907a8-333">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="907a8-333">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="907a8-334">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-334">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="907a8-335">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="907a8-335">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="907a8-336">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="907a8-336">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="907a8-337">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-337">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="907a8-338">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="907a8-338">The PutTodoItem method</span></span>

<span data-ttu-id="907a8-339">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="907a8-339">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="907a8-340">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="907a8-340">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="907a8-341">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="907a8-341">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="907a8-342">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="907a8-342">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="907a8-343">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="907a8-343">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="907a8-344">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-344">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="907a8-345">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-345">Test the PutTodoItem method</span></span>

<span data-ttu-id="907a8-346">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="907a8-346">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="907a8-347">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-347">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="907a8-348">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="907a8-348">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="907a8-349">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-349">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="907a8-350">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="907a8-350">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="907a8-352">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="907a8-352">The DeleteTodoItem method</span></span>

<span data-ttu-id="907a8-353">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="907a8-353">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="907a8-354">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-354">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="907a8-355">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="907a8-355">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="907a8-356">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-356">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="907a8-357">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-357">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="907a8-358">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-358">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="907a8-359">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="907a8-359">Prevent over-posting</span></span>

<span data-ttu-id="907a8-360">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="907a8-360">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="907a8-361">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="907a8-361">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="907a8-362">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="907a8-362">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="907a8-363">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="907a8-363">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="907a8-364">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="907a8-364">**DTO** is used in this article.</span></span>

<span data-ttu-id="907a8-365">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="907a8-365">A DTO may be used to:</span></span>

* <span data-ttu-id="907a8-366">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="907a8-366">Prevent over-posting.</span></span>
* <span data-ttu-id="907a8-367">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="907a8-367">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="907a8-368">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="907a8-368">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="907a8-369">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="907a8-369">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="907a8-370">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-370">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="907a8-371">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="907a8-371">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="907a8-372">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="907a8-372">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="907a8-373">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="907a8-373">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="907a8-374">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-374">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="907a8-375">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="907a8-375">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="907a8-376">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="907a8-376">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="907a8-377">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="907a8-377">Call the web API with JavaScript</span></span>

<span data-ttu-id="907a8-378">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-378">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="907a8-379">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="907a8-379">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="907a8-380">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="907a8-380">Create a web API project.</span></span>
> * <span data-ttu-id="907a8-381">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="907a8-381">Add a model class and a database context.</span></span>
> * <span data-ttu-id="907a8-382">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="907a8-382">Add a controller.</span></span>
> * <span data-ttu-id="907a8-383">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="907a8-383">Add CRUD methods.</span></span>
> * <span data-ttu-id="907a8-384">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="907a8-384">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="907a8-385">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="907a8-385">Specify return values.</span></span>
> * <span data-ttu-id="907a8-386">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="907a8-386">Call the web API with Postman.</span></span>
> * <span data-ttu-id="907a8-387">JavaScript を使用した Web API の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="907a8-387">Call the web API with JavaScript.</span></span>

<span data-ttu-id="907a8-388">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-388">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="907a8-389">概要</span><span class="sxs-lookup"><span data-stu-id="907a8-389">Overview</span></span>

<span data-ttu-id="907a8-390">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-390">This tutorial creates the following API:</span></span>

|<span data-ttu-id="907a8-391">API</span><span class="sxs-lookup"><span data-stu-id="907a8-391">API</span></span> | <span data-ttu-id="907a8-392">説明</span><span class="sxs-lookup"><span data-stu-id="907a8-392">Description</span></span> | <span data-ttu-id="907a8-393">要求本文</span><span class="sxs-lookup"><span data-stu-id="907a8-393">Request body</span></span> | <span data-ttu-id="907a8-394">応答本文</span><span class="sxs-lookup"><span data-stu-id="907a8-394">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="907a8-395">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="907a8-395">GET /api/TodoItems</span></span> | <span data-ttu-id="907a8-396">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="907a8-396">Get all to-do items</span></span> | <span data-ttu-id="907a8-397">None</span><span class="sxs-lookup"><span data-stu-id="907a8-397">None</span></span> | <span data-ttu-id="907a8-398">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="907a8-398">Array of to-do items</span></span>|
|<span data-ttu-id="907a8-399">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="907a8-399">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="907a8-400">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="907a8-400">Get an item by ID</span></span> | <span data-ttu-id="907a8-401">None</span><span class="sxs-lookup"><span data-stu-id="907a8-401">None</span></span> | <span data-ttu-id="907a8-402">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="907a8-402">To-do item</span></span>|
|<span data-ttu-id="907a8-403">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="907a8-403">POST /api/TodoItems</span></span> | <span data-ttu-id="907a8-404">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-404">Add a new item</span></span> | <span data-ttu-id="907a8-405">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="907a8-405">To-do item</span></span> | <span data-ttu-id="907a8-406">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="907a8-406">To-do item</span></span> |
|<span data-ttu-id="907a8-407">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="907a8-407">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="907a8-408">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="907a8-408">Update an existing item &nbsp;</span></span> | <span data-ttu-id="907a8-409">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="907a8-409">To-do item</span></span> | <span data-ttu-id="907a8-410">None</span><span class="sxs-lookup"><span data-stu-id="907a8-410">None</span></span> |
|<span data-ttu-id="907a8-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="907a8-411">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="907a8-412">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="907a8-412">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="907a8-413">None</span><span class="sxs-lookup"><span data-stu-id="907a8-413">None</span></span> | <span data-ttu-id="907a8-414">None</span><span class="sxs-lookup"><span data-stu-id="907a8-414">None</span></span>|

<span data-ttu-id="907a8-415">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="907a8-415">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="907a8-421">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="907a8-421">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-422">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="907a8-423">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="907a8-423">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="907a8-424">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-424">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="907a8-425">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="907a8-425">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-426">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-426">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="907a8-427">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-427">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="907a8-428">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-428">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="907a8-429">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-429">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="907a8-430">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="907a8-430">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="907a8-431">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-431">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="907a8-432">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="907a8-432">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="907a8-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="907a8-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="907a8-435">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="907a8-435">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="907a8-436">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="907a8-436">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="907a8-437">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-437">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="907a8-438">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="907a8-438">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="907a8-439">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-439">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="907a8-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="907a8-441">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-441">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="907a8-443">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-443">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="907a8-445">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="907a8-445">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="907a8-446">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-446">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="907a8-448">API のテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-448">Test the API</span></span>

<span data-ttu-id="907a8-449">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-449">The project template creates a `values` API.</span></span> <span data-ttu-id="907a8-450">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="907a8-450">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-451">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="907a8-452">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-452">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="907a8-453">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="907a8-453">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="907a8-454">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-454">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="907a8-455">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-455">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="907a8-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="907a8-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="907a8-457">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="907a8-457">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="907a8-458">ブラウザーで、次の URL に移動します: `https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="907a8-458">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="907a8-459">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="907a8-460">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="907a8-460">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="907a8-461">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="907a8-461">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="907a8-462">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-462">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="907a8-463">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="907a8-463">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="907a8-464">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-464">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="907a8-465">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-465">Add a model class</span></span>

<span data-ttu-id="907a8-466">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="907a8-466">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="907a8-467">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="907a8-467">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="907a8-469">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-469">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="907a8-470">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-470">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="907a8-471">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="907a8-471">Name the folder *Models*.</span></span>

* <span data-ttu-id="907a8-472">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="907a8-473">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-473">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="907a8-474">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-474">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="907a8-475">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="907a8-475">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="907a8-476">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-476">Add a folder named *Models*.</span></span>

* <span data-ttu-id="907a8-477">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-477">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="907a8-478">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-478">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="907a8-479">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-479">Right-click the project.</span></span> <span data-ttu-id="907a8-480">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-480">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="907a8-481">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="907a8-481">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="907a8-483">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-483">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="907a8-484">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-484">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="907a8-485">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-485">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="907a8-486">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="907a8-486">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="907a8-487">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-487">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="907a8-488">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-488">Add a database context</span></span>

<span data-ttu-id="907a8-489">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="907a8-489">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="907a8-490">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-490">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-491">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-491">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="907a8-492">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-492">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="907a8-493">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-493">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="907a8-494">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-494">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="907a8-495">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-495">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="907a8-496">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-496">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="907a8-497">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="907a8-497">Register the database context</span></span>

<span data-ttu-id="907a8-498">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-498">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="907a8-499">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="907a8-499">The container provides the service to controllers.</span></span>

<span data-ttu-id="907a8-500">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="907a8-500">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="907a8-501">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="907a8-501">The preceding code:</span></span>

* <span data-ttu-id="907a8-502">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="907a8-502">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="907a8-503">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-503">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="907a8-504">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-504">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="907a8-505">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-505">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-506">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="907a8-507">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="907a8-507">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="907a8-508">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-508">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="907a8-509">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-509">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="907a8-510">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-510">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="907a8-512">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-512">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="907a8-513">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-513">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="907a8-514">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-514">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="907a8-515">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="907a8-515">The preceding code:</span></span>

* <span data-ttu-id="907a8-516">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="907a8-516">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="907a8-517">クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="907a8-517">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="907a8-518">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="907a8-518">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="907a8-519">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-519">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="907a8-520">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="907a8-520">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="907a8-521">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-521">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="907a8-522">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="907a8-522">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="907a8-523">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-523">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="907a8-524">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-524">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="907a8-525">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-525">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="907a8-526">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-526">Add Get methods</span></span>

<span data-ttu-id="907a8-527">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-527">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="907a8-528">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="907a8-528">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="907a8-529">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="907a8-529">Stop the app if it's still running.</span></span> <span data-ttu-id="907a8-530">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="907a8-530">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="907a8-531">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="907a8-531">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="907a8-532">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="907a8-532">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="907a8-533">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-533">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="907a8-534">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="907a8-534">Routing and URL paths</span></span>

<span data-ttu-id="907a8-535">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="907a8-535">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="907a8-536">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-536">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="907a8-537">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="907a8-537">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="907a8-538">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-538">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="907a8-539">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="907a8-539">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="907a8-540">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="907a8-540">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="907a8-541">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-541">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="907a8-542">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="907a8-542">This sample doesn't use a template.</span></span> <span data-ttu-id="907a8-543">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-543">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="907a8-544">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="907a8-544">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="907a8-545">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-545">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="907a8-546">戻り値</span><span class="sxs-lookup"><span data-stu-id="907a8-546">Return values</span></span>

<span data-ttu-id="907a8-547">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型です](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="907a8-547">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="907a8-548">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="907a8-548">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="907a8-549">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-549">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="907a8-550">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-550">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="907a8-551">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="907a8-551">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="907a8-552">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-552">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="907a8-553">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="907a8-553">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="907a8-554">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="907a8-554">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="907a8-555">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-555">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="907a8-556">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-556">Test the GetTodoItems method</span></span>

<span data-ttu-id="907a8-557">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="907a8-557">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="907a8-558">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="907a8-558">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="907a8-559">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="907a8-559">Start the web app.</span></span>
* <span data-ttu-id="907a8-560">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="907a8-560">Start Postman.</span></span>
* <span data-ttu-id="907a8-561">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="907a8-561">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="907a8-562">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="907a8-562">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="907a8-563">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="907a8-563">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="907a8-564">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="907a8-564">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="907a8-565">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="907a8-565">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="907a8-566">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="907a8-566">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="907a8-567">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="907a8-567">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="907a8-568">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-568">Create a new request.</span></span>
  * <span data-ttu-id="907a8-569">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-569">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="907a8-570">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-570">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="907a8-571">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="907a8-571">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="907a8-572">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-572">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="907a8-573">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-573">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="907a8-575">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-575">Add a Create method</span></span>

<span data-ttu-id="907a8-576">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-576">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="907a8-577">[`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="907a8-577">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="907a8-578">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="907a8-578">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="907a8-579">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="907a8-579">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="907a8-580">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-580">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="907a8-581">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="907a8-581">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="907a8-582">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-582">Adds a `Location` header to the response.</span></span> <span data-ttu-id="907a8-583">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-583">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="907a8-584">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-584">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="907a8-585">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-585">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="907a8-586">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="907a8-586">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="907a8-587">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-587">Test the PostTodoItem method</span></span>

* <span data-ttu-id="907a8-588">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="907a8-588">Build the project.</span></span>
* <span data-ttu-id="907a8-589">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-589">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="907a8-590">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-590">Select the **Body** tab.</span></span>
* <span data-ttu-id="907a8-591">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-591">Select the **raw** radio button.</span></span>
* <span data-ttu-id="907a8-592">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-592">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="907a8-593">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="907a8-593">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="907a8-594">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-594">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="907a8-596">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-596">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="907a8-597">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-597">Test the location header URI</span></span>

* <span data-ttu-id="907a8-598">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-598">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="907a8-599">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="907a8-599">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="907a8-601">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-601">Set the method to GET.</span></span>
* <span data-ttu-id="907a8-602">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="907a8-602">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="907a8-603">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-603">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="907a8-604">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-604">Add a PutTodoItem method</span></span>

<span data-ttu-id="907a8-605">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-605">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="907a8-606">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="907a8-606">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="907a8-607">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="907a8-607">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="907a8-608">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="907a8-608">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="907a8-609">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="907a8-609">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="907a8-610">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-610">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="907a8-611">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-611">Test the PutTodoItem method</span></span>

<span data-ttu-id="907a8-612">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="907a8-612">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="907a8-613">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-613">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="907a8-614">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="907a8-614">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="907a8-615">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-615">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="907a8-616">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="907a8-616">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="907a8-618">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-618">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="907a8-619">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-619">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="907a8-620">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="907a8-620">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="907a8-621">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="907a8-621">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="907a8-622">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="907a8-622">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="907a8-623">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-623">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="907a8-624">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-624">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="907a8-625">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="907a8-625">Select **Send**.</span></span>

<span data-ttu-id="907a8-626">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="907a8-626">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="907a8-627">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-627">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="907a8-628">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="907a8-628">Call the web API with JavaScript</span></span>

<span data-ttu-id="907a8-629">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-629">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="907a8-630">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-630">jQuery initiates the request.</span></span> <span data-ttu-id="907a8-631">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-631">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="907a8-632">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-632">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="907a8-633">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="907a8-633">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="907a8-634">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-634">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="907a8-635">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-635">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="907a8-636">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="907a8-636">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="907a8-637">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="907a8-637">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="907a8-638">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="907a8-638">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="907a8-639">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="907a8-639">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="907a8-640">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="907a8-640">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="907a8-641">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="907a8-641">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="907a8-642">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="907a8-642">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="907a8-643">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="907a8-643">Get a list of to-do items</span></span>

<span data-ttu-id="907a8-644">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-644">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="907a8-645">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-645">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="907a8-646">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-646">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="907a8-647">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="907a8-647">Add a to-do item</span></span>

<span data-ttu-id="907a8-648">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-648">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="907a8-649">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-649">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="907a8-650">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="907a8-650">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="907a8-651">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="907a8-651">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="907a8-652">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="907a8-652">Update a to-do item</span></span>

<span data-ttu-id="907a8-653">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="907a8-653">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="907a8-654">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="907a8-654">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="907a8-655">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="907a8-655">Delete a to-do item</span></span>

<span data-ttu-id="907a8-656">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="907a8-656">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="907a8-657">Web API に認証サポートを追加</span><span class="sxs-lookup"><span data-stu-id="907a8-657">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="907a8-658">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="907a8-658">Additional resources</span></span>

<span data-ttu-id="907a8-659">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="907a8-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="907a8-660">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="907a8-661">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="907a8-661">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="907a8-662">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="907a8-662">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
