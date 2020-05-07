---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-web-api
ms.openlocfilehash: ddc14aba14e31c5530cda14b4792736da001246a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767240"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="dd52d-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="dd52d-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="dd52d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5)、[Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="dd52d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="dd52d-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dd52d-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dd52d-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-107">Create a web API project.</span></span>
> * <span data-ttu-id="dd52d-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="dd52d-109">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="dd52d-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="dd52d-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="dd52d-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="dd52d-111">Call the web API with Postman.</span></span>

<span data-ttu-id="dd52d-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="dd52d-113">概要</span><span class="sxs-lookup"><span data-stu-id="dd52d-113">Overview</span></span>

<span data-ttu-id="dd52d-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="dd52d-115">API</span><span class="sxs-lookup"><span data-stu-id="dd52d-115">API</span></span> | <span data-ttu-id="dd52d-116">説明</span><span class="sxs-lookup"><span data-stu-id="dd52d-116">Description</span></span> | <span data-ttu-id="dd52d-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="dd52d-117">Request body</span></span> | <span data-ttu-id="dd52d-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="dd52d-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="dd52d-119">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-119">Get all to-do items</span></span> | <span data-ttu-id="dd52d-120">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-120">None</span></span> | <span data-ttu-id="dd52d-121">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="dd52d-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="dd52d-122">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-122">Get an item by ID</span></span> | <span data-ttu-id="dd52d-123">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-123">None</span></span> | <span data-ttu-id="dd52d-124">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="dd52d-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="dd52d-125">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-125">Add a new item</span></span> | <span data-ttu-id="dd52d-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="dd52d-126">To-do item</span></span> | <span data-ttu-id="dd52d-127">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="dd52d-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="dd52d-128">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="dd52d-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="dd52d-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="dd52d-129">To-do item</span></span> | <span data-ttu-id="dd52d-130">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-130">None</span></span> |
|<span data-ttu-id="dd52d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="dd52d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="dd52d-132">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="dd52d-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="dd52d-133">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-133">None</span></span> | <span data-ttu-id="dd52d-134">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-134">None</span></span>|

<span data-ttu-id="dd52d-135">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-135">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="dd52d-141">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="dd52d-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd52d-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd52d-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="dd52d-145">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="dd52d-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd52d-147">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="dd52d-148">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="dd52d-149">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="dd52d-150">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="dd52d-151">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-151">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd52d-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd52d-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd52d-154">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="dd52d-155">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="dd52d-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="dd52d-157">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="dd52d-158">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-158">The preceding commands:</span></span>

  * <span data-ttu-id="dd52d-159">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="dd52d-160">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dd52d-162">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-162">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="dd52d-164">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-164">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="dd52d-166">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、\* *[.NET Core 3.1]* の **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-166">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="dd52d-167">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-167">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="dd52d-169">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-169">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="dd52d-170">API のテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-170">Test the API</span></span>

<span data-ttu-id="dd52d-171">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-171">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="dd52d-172">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-172">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd52d-174">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-174">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="dd52d-175">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-175">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="dd52d-176">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-176">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="dd52d-177">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-177">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd52d-178">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd52d-178">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dd52d-179">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="dd52d-180">ブラウザーで、次の URL に移動します: `https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="dd52d-180">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-181">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dd52d-182">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-182">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="dd52d-183">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-183">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="dd52d-184">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-184">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="dd52d-185">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="dd52d-185">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="dd52d-186">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-186">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="dd52d-187">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-187">Add a model class</span></span>

<span data-ttu-id="dd52d-188">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-188">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="dd52d-189">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-189">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd52d-191">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-191">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="dd52d-192">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-192">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dd52d-193">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-193">Name the folder *Models*.</span></span>

* <span data-ttu-id="dd52d-194">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-194">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dd52d-195">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-195">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="dd52d-196">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-196">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd52d-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd52d-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd52d-198">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-198">Add a folder named *Models*.</span></span>

* <span data-ttu-id="dd52d-199">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-199">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dd52d-201">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-201">Right-click the project.</span></span> <span data-ttu-id="dd52d-202">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-202">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dd52d-203">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-203">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="dd52d-205">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-205">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="dd52d-206">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-206">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="dd52d-207">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-207">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="dd52d-208">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-208">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="dd52d-209">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-209">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="dd52d-210">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-210">Add a database context</span></span>

<span data-ttu-id="dd52d-211">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-211">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="dd52d-212">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-212">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-213">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-213">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="dd52d-214">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="dd52d-214">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="dd52d-215">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-215">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="dd52d-216">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-216">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="dd52d-217">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-217">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="dd52d-218">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-218">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="dd52d-219">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-219">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="dd52d-221">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-221">Add the TodoContext database context</span></span>

* <span data-ttu-id="dd52d-222">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-222">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dd52d-223">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-223">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-224">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-224">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dd52d-225">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-225">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="dd52d-226">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-226">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="dd52d-227">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="dd52d-227">Register the database context</span></span>

<span data-ttu-id="dd52d-228">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-228">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dd52d-229">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-229">The container provides the service to controllers.</span></span>

<span data-ttu-id="dd52d-230">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-230">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="dd52d-231">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-231">The preceding code:</span></span>

* <span data-ttu-id="dd52d-232">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-232">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="dd52d-233">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-233">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="dd52d-234">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-234">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="dd52d-235">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="dd52d-235">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-236">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd52d-237">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-237">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="dd52d-238">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-238">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="dd52d-239">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-239">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="dd52d-240">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-240">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="dd52d-241">**モデル クラス**で **[TodoItem (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-241">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="dd52d-242">**データ コンテキスト クラス**で **[TodoContext (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-242">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="dd52d-243">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-243">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-244">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-244">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dd52d-245">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-245">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="dd52d-246">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-246">The preceding commands:</span></span>

* <span data-ttu-id="dd52d-247">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-247">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="dd52d-248">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-248">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="dd52d-249">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-249">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="dd52d-250">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-250">The generated code:</span></span>

* <span data-ttu-id="dd52d-251">クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-251">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="dd52d-252">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-252">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="dd52d-253">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-253">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="dd52d-254">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-254">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="dd52d-255">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-255">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="dd52d-256">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-256">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="dd52d-257">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-257">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="dd52d-258">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="dd52d-258">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="dd52d-259">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-259">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="dd52d-260">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="dd52d-260">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="dd52d-261">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="dd52d-261">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="dd52d-262">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-262">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="dd52d-263">[`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-263">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="dd52d-264">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-264">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="dd52d-265"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="dd52d-265">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="dd52d-266">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-266">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="dd52d-267">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="dd52d-267">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="dd52d-268">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-268">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="dd52d-269">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-269">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="dd52d-270">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-270">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="dd52d-271">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-271">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="dd52d-272">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-272">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="dd52d-273">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="dd52d-273">Install Postman</span></span>

<span data-ttu-id="dd52d-274">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-274">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="dd52d-275">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-275">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="dd52d-276">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-276">Start the web app.</span></span>
* <span data-ttu-id="dd52d-277">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-277">Start Postman.</span></span>
* <span data-ttu-id="dd52d-278">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-278">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="dd52d-279">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-279">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="dd52d-280">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-280">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="dd52d-281">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-281">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="dd52d-282">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-282">Create a new request.</span></span>
* <span data-ttu-id="dd52d-283">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-283">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="dd52d-284">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-284">Select the **Body** tab.</span></span>
* <span data-ttu-id="dd52d-285">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-285">Select the **raw** radio button.</span></span>
* <span data-ttu-id="dd52d-286">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-286">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="dd52d-287">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-287">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="dd52d-288">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-288">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="dd52d-290">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-290">Test the location header URI</span></span>

* <span data-ttu-id="dd52d-291">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-291">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="dd52d-292">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-292">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="dd52d-294">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-294">Set the method to GET.</span></span>
* <span data-ttu-id="dd52d-295">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="dd52d-295">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="dd52d-296">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-296">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="dd52d-297">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="dd52d-297">Examine the GET methods</span></span>

<span data-ttu-id="dd52d-298">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-298">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="dd52d-299">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-299">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="dd52d-300">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-300">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="dd52d-301">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-301">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="dd52d-302">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="dd52d-302">Test Get with Postman</span></span>

* <span data-ttu-id="dd52d-303">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-303">Create a new request.</span></span>
* <span data-ttu-id="dd52d-304">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-304">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="dd52d-305">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-305">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="dd52d-306">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-306">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="dd52d-307">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-307">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="dd52d-308">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-308">Select **Send**.</span></span>

<span data-ttu-id="dd52d-309">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-309">This app uses an in-memory database.</span></span> <span data-ttu-id="dd52d-310">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="dd52d-310">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="dd52d-311">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-311">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="dd52d-312">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="dd52d-312">Routing and URL paths</span></span>

<span data-ttu-id="dd52d-313">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-313">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="dd52d-314">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-314">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="dd52d-315">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-315">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="dd52d-316">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-316">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="dd52d-317">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="dd52d-317">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="dd52d-318">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="dd52d-318">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="dd52d-319">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-319">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="dd52d-320">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="dd52d-320">This sample doesn't use a template.</span></span> <span data-ttu-id="dd52d-321">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-321">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="dd52d-322">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="dd52d-322">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="dd52d-323">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-323">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="dd52d-324">戻り値</span><span class="sxs-lookup"><span data-stu-id="dd52d-324">Return values</span></span>

<span data-ttu-id="dd52d-325">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型です](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="dd52d-325">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="dd52d-326">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-326">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="dd52d-327">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-327">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="dd52d-328">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-328">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="dd52d-329">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-329">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="dd52d-330">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-330">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="dd52d-331">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-331">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="dd52d-332">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-332">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="dd52d-333">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-333">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="dd52d-334">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="dd52d-334">The PutTodoItem method</span></span>

<span data-ttu-id="dd52d-335">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-335">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="dd52d-336">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-336">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="dd52d-337">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-337">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="dd52d-338">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-338">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="dd52d-339">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-339">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="dd52d-340">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-340">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="dd52d-341">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-341">Test the PutTodoItem method</span></span>

<span data-ttu-id="dd52d-342">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-342">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="dd52d-343">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-343">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="dd52d-344">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-344">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="dd52d-345">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-345">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="dd52d-346">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-346">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="dd52d-348">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="dd52d-348">The DeleteTodoItem method</span></span>

<span data-ttu-id="dd52d-349">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-349">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="dd52d-350">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-350">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="dd52d-351">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-351">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="dd52d-352">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-352">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="dd52d-353">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-353">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="dd52d-354">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-354">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="dd52d-355">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="dd52d-355">Prevent over-posting</span></span>

<span data-ttu-id="dd52d-356">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-356">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="dd52d-357">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-357">Productions apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="dd52d-358">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-358">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="dd52d-359">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-359">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="dd52d-360">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-360">**DTO** is used in this article.</span></span>

<span data-ttu-id="dd52d-361">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-361">A DTO may be used to:</span></span>

* <span data-ttu-id="dd52d-362">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-362">Prevent over-posting.</span></span>
* <span data-ttu-id="dd52d-363">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="dd52d-363">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="dd52d-364">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-364">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="dd52d-365">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-365">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="dd52d-366">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-366">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="dd52d-367">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-367">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="dd52d-368">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-368">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="dd52d-369">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-369">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="dd52d-370">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-370">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="dd52d-371">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-371">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="dd52d-372">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-372">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="dd52d-373">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="dd52d-373">Call the web API with JavaScript</span></span>

<span data-ttu-id="dd52d-374">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-374">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dd52d-375">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-375">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dd52d-376">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-376">Create a web API project.</span></span>
> * <span data-ttu-id="dd52d-377">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-377">Add a model class and a database context.</span></span>
> * <span data-ttu-id="dd52d-378">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-378">Add a controller.</span></span>
> * <span data-ttu-id="dd52d-379">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-379">Add CRUD methods.</span></span>
> * <span data-ttu-id="dd52d-380">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-380">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="dd52d-381">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="dd52d-381">Specify return values.</span></span>
> * <span data-ttu-id="dd52d-382">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="dd52d-382">Call the web API with Postman.</span></span>
> * <span data-ttu-id="dd52d-383">JavaScript を使用した Web API の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="dd52d-383">Call the web API with JavaScript.</span></span>

<span data-ttu-id="dd52d-384">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-384">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="dd52d-385">概要</span><span class="sxs-lookup"><span data-stu-id="dd52d-385">Overview</span></span>

<span data-ttu-id="dd52d-386">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-386">This tutorial creates the following API:</span></span>

|<span data-ttu-id="dd52d-387">API</span><span class="sxs-lookup"><span data-stu-id="dd52d-387">API</span></span> | <span data-ttu-id="dd52d-388">説明</span><span class="sxs-lookup"><span data-stu-id="dd52d-388">Description</span></span> | <span data-ttu-id="dd52d-389">要求本文</span><span class="sxs-lookup"><span data-stu-id="dd52d-389">Request body</span></span> | <span data-ttu-id="dd52d-390">応答本文</span><span class="sxs-lookup"><span data-stu-id="dd52d-390">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="dd52d-391">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="dd52d-391">GET /api/TodoItems</span></span> | <span data-ttu-id="dd52d-392">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-392">Get all to-do items</span></span> | <span data-ttu-id="dd52d-393">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-393">None</span></span> | <span data-ttu-id="dd52d-394">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="dd52d-394">Array of to-do items</span></span>|
|<span data-ttu-id="dd52d-395">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="dd52d-395">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="dd52d-396">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-396">Get an item by ID</span></span> | <span data-ttu-id="dd52d-397">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-397">None</span></span> | <span data-ttu-id="dd52d-398">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="dd52d-398">To-do item</span></span>|
|<span data-ttu-id="dd52d-399">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="dd52d-399">POST /api/TodoItems</span></span> | <span data-ttu-id="dd52d-400">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-400">Add a new item</span></span> | <span data-ttu-id="dd52d-401">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="dd52d-401">To-do item</span></span> | <span data-ttu-id="dd52d-402">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="dd52d-402">To-do item</span></span> |
|<span data-ttu-id="dd52d-403">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="dd52d-403">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="dd52d-404">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="dd52d-404">Update an existing item &nbsp;</span></span> | <span data-ttu-id="dd52d-405">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="dd52d-405">To-do item</span></span> | <span data-ttu-id="dd52d-406">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-406">None</span></span> |
|<span data-ttu-id="dd52d-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="dd52d-407">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="dd52d-408">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="dd52d-408">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="dd52d-409">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-409">None</span></span> | <span data-ttu-id="dd52d-410">None</span><span class="sxs-lookup"><span data-stu-id="dd52d-410">None</span></span>|

<span data-ttu-id="dd52d-411">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-411">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="dd52d-417">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="dd52d-417">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-418">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd52d-419">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd52d-419">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-420">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="dd52d-421">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="dd52d-421">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd52d-423">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-423">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="dd52d-424">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-424">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="dd52d-425">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-425">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="dd52d-426">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-426">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="dd52d-427">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-427">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="dd52d-428">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-428">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd52d-430">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd52d-430">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd52d-431">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-431">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="dd52d-432">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-432">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="dd52d-433">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-433">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="dd52d-434">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-434">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="dd52d-435">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-435">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dd52d-437">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-437">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="dd52d-439">**[.NET Core]** > **[アプリ]** > **[API]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-439">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS の [新しいプロジェクト] ダイアログ](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="dd52d-441">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログ ボックスで、既定の**ターゲット フレームワーク** \* *.NET Core 2.2* を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-441">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="dd52d-442">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-442">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="dd52d-444">API のテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-444">Test the API</span></span>

<span data-ttu-id="dd52d-445">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-445">The project template creates a `values` API.</span></span> <span data-ttu-id="dd52d-446">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-446">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-447">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-447">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dd52d-448">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-448">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="dd52d-449">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-449">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="dd52d-450">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-450">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="dd52d-451">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-451">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd52d-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd52d-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="dd52d-453">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-453">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="dd52d-454">ブラウザーで、次の URL に移動します: `https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="dd52d-454">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-455">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="dd52d-456">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-456">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="dd52d-457">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-457">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="dd52d-458">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-458">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="dd52d-459">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="dd52d-459">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="dd52d-460">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-460">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="dd52d-461">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-461">Add a model class</span></span>

<span data-ttu-id="dd52d-462">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-462">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="dd52d-463">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-463">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-464">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-464">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd52d-465">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-465">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="dd52d-466">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-466">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dd52d-467">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-467">Name the folder *Models*.</span></span>

* <span data-ttu-id="dd52d-468">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-468">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dd52d-469">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-469">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="dd52d-470">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-470">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="dd52d-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="dd52d-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="dd52d-472">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-472">Add a folder named *Models*.</span></span>

* <span data-ttu-id="dd52d-473">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-473">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-474">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="dd52d-475">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-475">Right-click the project.</span></span> <span data-ttu-id="dd52d-476">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-476">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="dd52d-477">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-477">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="dd52d-479">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-479">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="dd52d-480">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-480">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="dd52d-481">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-481">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="dd52d-482">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-482">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="dd52d-483">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-483">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="dd52d-484">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-484">Add a database context</span></span>

<span data-ttu-id="dd52d-485">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-485">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="dd52d-486">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-486">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd52d-488">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="dd52d-489">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-489">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-490">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-490">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dd52d-491">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-491">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="dd52d-492">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-492">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="dd52d-493">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="dd52d-493">Register the database context</span></span>

<span data-ttu-id="dd52d-494">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-494">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dd52d-495">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-495">The container provides the service to controllers.</span></span>

<span data-ttu-id="dd52d-496">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-496">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="dd52d-497">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-497">The preceding code:</span></span>

* <span data-ttu-id="dd52d-498">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-498">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="dd52d-499">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-499">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="dd52d-500">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-500">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="dd52d-501">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-501">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-502">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-502">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd52d-503">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-503">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="dd52d-504">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-504">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="dd52d-505">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-505">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="dd52d-506">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-506">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-508">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-508">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dd52d-509">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-509">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="dd52d-510">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-510">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="dd52d-511">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-511">The preceding code:</span></span>

* <span data-ttu-id="dd52d-512">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-512">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="dd52d-513">クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-513">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="dd52d-514">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-514">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="dd52d-515">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-515">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="dd52d-516">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-516">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="dd52d-517">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-517">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="dd52d-518">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-518">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="dd52d-519">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-519">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="dd52d-520">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-520">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="dd52d-521">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-521">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="dd52d-522">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-522">Add Get methods</span></span>

<span data-ttu-id="dd52d-523">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-523">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="dd52d-524">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-524">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="dd52d-525">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-525">Stop the app if it's still running.</span></span> <span data-ttu-id="dd52d-526">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-526">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="dd52d-527">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-527">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="dd52d-528">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-528">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="dd52d-529">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-529">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="dd52d-530">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="dd52d-530">Routing and URL paths</span></span>

<span data-ttu-id="dd52d-531">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-531">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="dd52d-532">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-532">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="dd52d-533">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-533">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="dd52d-534">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-534">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="dd52d-535">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="dd52d-535">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="dd52d-536">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="dd52d-536">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="dd52d-537">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-537">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="dd52d-538">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="dd52d-538">This sample doesn't use a template.</span></span> <span data-ttu-id="dd52d-539">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-539">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="dd52d-540">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="dd52d-540">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="dd52d-541">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-541">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="dd52d-542">戻り値</span><span class="sxs-lookup"><span data-stu-id="dd52d-542">Return values</span></span>

<span data-ttu-id="dd52d-543">`GetTodoItems` メソッドと `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型です](xref:web-api/action-return-types#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="dd52d-543">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="dd52d-544">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-544">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="dd52d-545">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-545">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="dd52d-546">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-546">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="dd52d-547">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-547">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="dd52d-548">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-548">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="dd52d-549">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-549">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="dd52d-550">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-550">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="dd52d-551">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-551">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="dd52d-552">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-552">Test the GetTodoItems method</span></span>

<span data-ttu-id="dd52d-553">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-553">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="dd52d-554">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-554">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="dd52d-555">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-555">Start the web app.</span></span>
* <span data-ttu-id="dd52d-556">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-556">Start Postman.</span></span>
* <span data-ttu-id="dd52d-557">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-557">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dd52d-558">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dd52d-558">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="dd52d-559">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-559">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dd52d-560">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dd52d-560">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="dd52d-561">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-561">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="dd52d-562">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-562">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="dd52d-563">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-563">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="dd52d-564">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-564">Create a new request.</span></span>
  * <span data-ttu-id="dd52d-565">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-565">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="dd52d-566">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-566">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="dd52d-567">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-567">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="dd52d-568">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-568">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="dd52d-569">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-569">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="dd52d-571">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-571">Add a Create method</span></span>

<span data-ttu-id="dd52d-572">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-572">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="dd52d-573">[`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="dd52d-573">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="dd52d-574">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-574">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="dd52d-575">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="dd52d-575">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="dd52d-576">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-576">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="dd52d-577">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="dd52d-577">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="dd52d-578">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-578">Adds a `Location` header to the response.</span></span> <span data-ttu-id="dd52d-579">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-579">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="dd52d-580">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-580">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="dd52d-581">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-581">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="dd52d-582">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-582">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="dd52d-583">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-583">Test the PostTodoItem method</span></span>

* <span data-ttu-id="dd52d-584">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-584">Build the project.</span></span>
* <span data-ttu-id="dd52d-585">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-585">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="dd52d-586">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-586">Select the **Body** tab.</span></span>
* <span data-ttu-id="dd52d-587">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-587">Select the **raw** radio button.</span></span>
* <span data-ttu-id="dd52d-588">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-588">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="dd52d-589">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-589">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="dd52d-590">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-590">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="dd52d-592">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-592">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="dd52d-593">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-593">Test the location header URI</span></span>

* <span data-ttu-id="dd52d-594">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-594">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="dd52d-595">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="dd52d-595">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="dd52d-597">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-597">Set the method to GET.</span></span>
* <span data-ttu-id="dd52d-598">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="dd52d-598">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="dd52d-599">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-599">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="dd52d-600">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-600">Add a PutTodoItem method</span></span>

<span data-ttu-id="dd52d-601">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-601">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="dd52d-602">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-602">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="dd52d-603">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-603">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="dd52d-604">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-604">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="dd52d-605">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-605">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="dd52d-606">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-606">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="dd52d-607">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-607">Test the PutTodoItem method</span></span>

<span data-ttu-id="dd52d-608">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-608">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="dd52d-609">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-609">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="dd52d-610">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-610">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="dd52d-611">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-611">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="dd52d-612">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-612">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="dd52d-614">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-614">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="dd52d-615">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-615">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="dd52d-616">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-616">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="dd52d-617">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="dd52d-617">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="dd52d-618">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-618">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="dd52d-619">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-619">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="dd52d-620">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-620">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="dd52d-621">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-621">Select **Send**.</span></span>

<span data-ttu-id="dd52d-622">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-622">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="dd52d-623">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-623">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="dd52d-624">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="dd52d-624">Call the web API with JavaScript</span></span>

<span data-ttu-id="dd52d-625">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-625">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="dd52d-626">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-626">jQuery initiates the request.</span></span> <span data-ttu-id="dd52d-627">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-627">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="dd52d-628">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-628">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="dd52d-629">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-629">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="dd52d-630">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-630">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="dd52d-631">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-631">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="dd52d-632">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-632">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="dd52d-633">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-633">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="dd52d-634">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-634">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="dd52d-635">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-635">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="dd52d-636">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-636">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="dd52d-637">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-637">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="dd52d-638">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-638">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="dd52d-639">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="dd52d-639">Get a list of to-do items</span></span>

<span data-ttu-id="dd52d-640">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-640">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="dd52d-641">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-641">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="dd52d-642">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-642">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="dd52d-643">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-643">Add a to-do item</span></span>

<span data-ttu-id="dd52d-644">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-644">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="dd52d-645">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-645">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="dd52d-646">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="dd52d-646">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="dd52d-647">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-647">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="dd52d-648">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="dd52d-648">Update a to-do item</span></span>

<span data-ttu-id="dd52d-649">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="dd52d-649">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="dd52d-650">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="dd52d-650">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="dd52d-651">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="dd52d-651">Delete a to-do item</span></span>

<span data-ttu-id="dd52d-652">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-652">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="dd52d-653">Web API に認証サポートを追加</span><span class="sxs-lookup"><span data-stu-id="dd52d-653">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="dd52d-654">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="dd52d-654">Additional resources</span></span>

<span data-ttu-id="dd52d-655">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="dd52d-655">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="dd52d-656">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-656">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="dd52d-657">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="dd52d-657">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="dd52d-658">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="dd52d-658">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
