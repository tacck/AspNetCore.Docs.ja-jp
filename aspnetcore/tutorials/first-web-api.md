---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 2/25/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-web-api
ms.openlocfilehash: 79f36168d0430ceee3794cfb5a4e29f3671ac73f
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212622"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="55338-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="55338-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="55338-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5)、[Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="55338-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="55338-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="55338-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="55338-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="55338-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="55338-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="55338-107">Create a web API project.</span></span>
> * <span data-ttu-id="55338-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="55338-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="55338-109">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="55338-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="55338-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="55338-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="55338-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="55338-111">Call the web API with Postman.</span></span>

<span data-ttu-id="55338-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="55338-113">概要</span><span class="sxs-lookup"><span data-stu-id="55338-113">Overview</span></span>

<span data-ttu-id="55338-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="55338-115">API</span><span class="sxs-lookup"><span data-stu-id="55338-115">API</span></span> | <span data-ttu-id="55338-116">説明</span><span class="sxs-lookup"><span data-stu-id="55338-116">Description</span></span> | <span data-ttu-id="55338-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="55338-117">Request body</span></span> | <span data-ttu-id="55338-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="55338-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="55338-119">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="55338-119">Get all to-do items</span></span> | <span data-ttu-id="55338-120">None</span><span class="sxs-lookup"><span data-stu-id="55338-120">None</span></span> | <span data-ttu-id="55338-121">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="55338-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="55338-122">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="55338-122">Get an item by ID</span></span> | <span data-ttu-id="55338-123">None</span><span class="sxs-lookup"><span data-stu-id="55338-123">None</span></span> | <span data-ttu-id="55338-124">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="55338-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="55338-125">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-125">Add a new item</span></span> | <span data-ttu-id="55338-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="55338-126">To-do item</span></span> | <span data-ttu-id="55338-127">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="55338-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="55338-128">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="55338-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="55338-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="55338-129">To-do item</span></span> | <span data-ttu-id="55338-130">None</span><span class="sxs-lookup"><span data-stu-id="55338-130">None</span></span> |
|<span data-ttu-id="55338-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="55338-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="55338-132">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="55338-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="55338-133">None</span><span class="sxs-lookup"><span data-stu-id="55338-133">None</span></span> | <span data-ttu-id="55338-134">None</span><span class="sxs-lookup"><span data-stu-id="55338-134">None</span></span>|

<span data-ttu-id="55338-135">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="55338-135">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="55338-141">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="55338-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="55338-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55338-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55338-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="55338-145">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="55338-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55338-147">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="55338-148">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="55338-149">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="55338-150">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="55338-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="55338-151">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-151">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="55338-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55338-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="55338-154">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="55338-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="55338-155">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="55338-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="55338-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="55338-157">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="55338-158">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="55338-158">The preceding commands:</span></span>

  * <span data-ttu-id="55338-159">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="55338-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="55338-160">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55338-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="55338-162">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-162">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="55338-164">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="55338-165">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![macOS での API テンプレートの選択](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="55338-167">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 3.x **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-167">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="55338-168">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-168">Select **Next**.</span></span>

* <span data-ttu-id="55338-169">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="55338-171">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="55338-172">API のテスト</span><span class="sxs-lookup"><span data-stu-id="55338-172">Test the API</span></span>

<span data-ttu-id="55338-173">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="55338-174">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="55338-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="55338-176">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="55338-177">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="55338-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="55338-178">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="55338-179">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="55338-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55338-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="55338-181">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="55338-182">ブラウザーで、次の URL に移動します: `https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="55338-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55338-183">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="55338-184">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="55338-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="55338-185">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="55338-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="55338-186">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="55338-187">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="55338-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="55338-188">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="55338-189">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="55338-189">Add a model class</span></span>

<span data-ttu-id="55338-190">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="55338-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="55338-191">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="55338-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55338-193">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="55338-194">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="55338-195">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="55338-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="55338-196">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="55338-197">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="55338-198">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="55338-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55338-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="55338-200">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="55338-201">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55338-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="55338-203">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-203">Right-click the project.</span></span> <span data-ttu-id="55338-204">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="55338-205">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="55338-205">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="55338-207">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="55338-208">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="55338-209">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="55338-210">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="55338-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="55338-211">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="55338-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="55338-212">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="55338-212">Add a database context</span></span>

<span data-ttu-id="55338-213">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="55338-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="55338-214">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="55338-216">Microsoft.EntityFrameworkCore.SqlServer を追加する</span><span class="sxs-lookup"><span data-stu-id="55338-216">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="55338-217">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="55338-218">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="55338-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="55338-219">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="55338-220">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="55338-221">前の手順を使用して `Microsoft.EntityFrameworkCore.InMemory` NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-221">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="55338-223">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="55338-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="55338-224">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="55338-225">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="55338-226">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="55338-227">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="55338-228">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="55338-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="55338-229">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="55338-229">Register the database context</span></span>

<span data-ttu-id="55338-230">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="55338-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="55338-231">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="55338-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="55338-232">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="55338-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="55338-233">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="55338-233">The preceding code:</span></span>

* <span data-ttu-id="55338-234">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="55338-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="55338-235">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="55338-236">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="55338-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="55338-237">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="55338-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55338-239">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="55338-240">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="55338-241">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="55338-242">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="55338-243">**モデル クラス**で **[TodoItem (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="55338-244">**データ コンテキスト クラス**で **[TodoContext (TodoApi.Models)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="55338-245">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="55338-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="55338-246">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="55338-247">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="55338-248">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="55338-248">The preceding commands:</span></span>

* <span data-ttu-id="55338-249">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="55338-250">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="55338-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="55338-251">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="55338-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="55338-252">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="55338-252">The generated code:</span></span>

* <span data-ttu-id="55338-253">クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="55338-253">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="55338-254">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="55338-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="55338-255">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="55338-256">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="55338-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="55338-257">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="55338-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="55338-258">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="55338-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="55338-259">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="55338-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="55338-260">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="55338-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="55338-261">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="55338-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="55338-262">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="55338-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="55338-263">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="55338-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="55338-264">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="55338-265">[`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="55338-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="55338-266">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="55338-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="55338-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="55338-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="55338-268">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="55338-269">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="55338-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="55338-270">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="55338-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="55338-271">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="55338-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="55338-272">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="55338-273">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="55338-274">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="55338-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="55338-275">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="55338-275">Install Postman</span></span>

<span data-ttu-id="55338-276">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="55338-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="55338-277">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="55338-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="55338-278">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="55338-278">Start the web app.</span></span>
* <span data-ttu-id="55338-279">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="55338-279">Start Postman.</span></span>
* <span data-ttu-id="55338-280">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="55338-280">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="55338-281">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="55338-281">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="55338-282">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="55338-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="55338-283">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="55338-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="55338-284">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-284">Create a new request.</span></span>
* <span data-ttu-id="55338-285">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="55338-286">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="55338-287">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="55338-288">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="55338-289">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="55338-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="55338-290">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-290">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="55338-292">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="55338-292">Test the location header URI</span></span>

* <span data-ttu-id="55338-293">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="55338-294">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="55338-294">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="55338-296">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-296">Set the method to GET.</span></span>
* <span data-ttu-id="55338-297">URI を貼り付けます (たとえば、`https://localhost:5001/api/TodoItems/1`)。</span><span class="sxs-lookup"><span data-stu-id="55338-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="55338-298">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="55338-299">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="55338-299">Examine the GET methods</span></span>

<span data-ttu-id="55338-300">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="55338-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="55338-301">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="55338-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="55338-302">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="55338-302">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="55338-303">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="55338-304">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="55338-304">Test Get with Postman</span></span>

* <span data-ttu-id="55338-305">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-305">Create a new request.</span></span>
* <span data-ttu-id="55338-306">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="55338-307">要求 URL を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="55338-308">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="55338-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="55338-309">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="55338-310">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-310">Select **Send**.</span></span>

<span data-ttu-id="55338-311">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="55338-311">This app uses an in-memory database.</span></span> <span data-ttu-id="55338-312">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="55338-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="55338-313">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="55338-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="55338-314">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="55338-314">Routing and URL paths</span></span>

<span data-ttu-id="55338-315">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="55338-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="55338-316">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="55338-317">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="55338-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="55338-318">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="55338-319">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="55338-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="55338-320">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="55338-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="55338-321">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="55338-322">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="55338-322">This sample doesn't use a template.</span></span> <span data-ttu-id="55338-323">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="55338-324">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="55338-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="55338-325">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="55338-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="55338-326">戻り値</span><span class="sxs-lookup"><span data-stu-id="55338-326">Return values</span></span>

<span data-ttu-id="55338-327">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="55338-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="55338-328">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="55338-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="55338-329">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="55338-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="55338-330">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="55338-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="55338-331">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="55338-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="55338-332">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="55338-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="55338-333">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="55338-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="55338-334">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="55338-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="55338-335">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="55338-336">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="55338-336">The PutTodoItem method</span></span>

<span data-ttu-id="55338-337">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="55338-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="55338-338">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="55338-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="55338-339">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="55338-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="55338-340">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="55338-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="55338-341">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="55338-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="55338-342">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="55338-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="55338-343">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="55338-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="55338-344">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="55338-344">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="55338-345">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="55338-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="55338-346">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="55338-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="55338-347">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="55338-348">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="55338-348">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="55338-350">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="55338-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="55338-351">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="55338-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="55338-352">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="55338-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="55338-353">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="55338-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="55338-354">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="55338-355">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-355">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="55338-356">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-356">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="55338-357">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="55338-357">Prevent over-posting</span></span>

<span data-ttu-id="55338-358">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="55338-358">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="55338-359">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="55338-359">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="55338-360">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="55338-360">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="55338-361">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="55338-361">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="55338-362">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="55338-362">**DTO** is used in this article.</span></span>

<span data-ttu-id="55338-363">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="55338-363">A DTO may be used to:</span></span>

* <span data-ttu-id="55338-364">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="55338-364">Prevent over-posting.</span></span>
* <span data-ttu-id="55338-365">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="55338-365">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="55338-366">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="55338-366">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="55338-367">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="55338-367">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="55338-368">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="55338-368">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="55338-369">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="55338-369">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="55338-370">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="55338-370">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="55338-371">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="55338-371">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="55338-372">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-372">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="55338-373">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="55338-373">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="55338-374">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="55338-374">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="55338-375">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="55338-375">Call the web API with JavaScript</span></span>

<span data-ttu-id="55338-376">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-376">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="55338-377">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="55338-377">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="55338-378">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="55338-378">Create a web API project.</span></span>
> * <span data-ttu-id="55338-379">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="55338-379">Add a model class and a database context.</span></span>
> * <span data-ttu-id="55338-380">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="55338-380">Add a controller.</span></span>
> * <span data-ttu-id="55338-381">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="55338-381">Add CRUD methods.</span></span>
> * <span data-ttu-id="55338-382">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="55338-382">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="55338-383">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="55338-383">Specify return values.</span></span>
> * <span data-ttu-id="55338-384">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="55338-384">Call the web API with Postman.</span></span>
> * <span data-ttu-id="55338-385">JavaScript を使用した Web API の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="55338-385">Call the web API with JavaScript.</span></span>

<span data-ttu-id="55338-386">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-386">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="55338-387">概要</span><span class="sxs-lookup"><span data-stu-id="55338-387">Overview</span></span>

<span data-ttu-id="55338-388">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-388">This tutorial creates the following API:</span></span>

|<span data-ttu-id="55338-389">API</span><span class="sxs-lookup"><span data-stu-id="55338-389">API</span></span> | <span data-ttu-id="55338-390">説明</span><span class="sxs-lookup"><span data-stu-id="55338-390">Description</span></span> | <span data-ttu-id="55338-391">要求本文</span><span class="sxs-lookup"><span data-stu-id="55338-391">Request body</span></span> | <span data-ttu-id="55338-392">応答本文</span><span class="sxs-lookup"><span data-stu-id="55338-392">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="55338-393">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="55338-393">GET /api/TodoItems</span></span> | <span data-ttu-id="55338-394">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="55338-394">Get all to-do items</span></span> | <span data-ttu-id="55338-395">None</span><span class="sxs-lookup"><span data-stu-id="55338-395">None</span></span> | <span data-ttu-id="55338-396">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="55338-396">Array of to-do items</span></span>|
|<span data-ttu-id="55338-397">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="55338-397">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="55338-398">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="55338-398">Get an item by ID</span></span> | <span data-ttu-id="55338-399">None</span><span class="sxs-lookup"><span data-stu-id="55338-399">None</span></span> | <span data-ttu-id="55338-400">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="55338-400">To-do item</span></span>|
|<span data-ttu-id="55338-401">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="55338-401">POST /api/TodoItems</span></span> | <span data-ttu-id="55338-402">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-402">Add a new item</span></span> | <span data-ttu-id="55338-403">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="55338-403">To-do item</span></span> | <span data-ttu-id="55338-404">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="55338-404">To-do item</span></span> |
|<span data-ttu-id="55338-405">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="55338-405">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="55338-406">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="55338-406">Update an existing item &nbsp;</span></span> | <span data-ttu-id="55338-407">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="55338-407">To-do item</span></span> | <span data-ttu-id="55338-408">None</span><span class="sxs-lookup"><span data-stu-id="55338-408">None</span></span> |
|<span data-ttu-id="55338-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="55338-409">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="55338-410">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="55338-410">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="55338-411">None</span><span class="sxs-lookup"><span data-stu-id="55338-411">None</span></span> | <span data-ttu-id="55338-412">None</span><span class="sxs-lookup"><span data-stu-id="55338-412">None</span></span>|

<span data-ttu-id="55338-413">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="55338-413">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="55338-419">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="55338-419">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-420">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="55338-421">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55338-421">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55338-422">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="55338-423">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="55338-423">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-424">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55338-425">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-425">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="55338-426">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-426">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="55338-427">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-427">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="55338-428">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="55338-428">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="55338-429">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-429">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="55338-430">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="55338-430">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="55338-432">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55338-432">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="55338-433">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="55338-433">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="55338-434">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="55338-434">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="55338-435">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-435">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="55338-436">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="55338-436">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="55338-437">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-437">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55338-438">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="55338-439">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-439">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="55338-441">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-441">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="55338-442">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-442">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="55338-443">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 2.x **[ターゲット フレームワーク]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-443">In the **Configure your new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="55338-444">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-444">Select **Next**.</span></span>

* <span data-ttu-id="55338-445">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-445">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="55338-447">API のテスト</span><span class="sxs-lookup"><span data-stu-id="55338-447">Test the API</span></span>

<span data-ttu-id="55338-448">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-448">The project template creates a `values` API.</span></span> <span data-ttu-id="55338-449">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="55338-449">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-450">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-450">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="55338-451">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-451">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="55338-452">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="55338-452">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="55338-453">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-453">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="55338-454">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-454">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="55338-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55338-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="55338-456">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="55338-456">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="55338-457">ブラウザーで、次の URL に移動します: `https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="55338-457">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55338-458">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="55338-459">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="55338-459">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="55338-460">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="55338-460">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="55338-461">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-461">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="55338-462">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="55338-462">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="55338-463">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-463">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="55338-464">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="55338-464">Add a model class</span></span>

<span data-ttu-id="55338-465">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="55338-465">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="55338-466">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="55338-466">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-467">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55338-468">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-468">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="55338-469">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-469">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="55338-470">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="55338-470">Name the folder *Models*.</span></span>

* <span data-ttu-id="55338-471">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-471">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="55338-472">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-472">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="55338-473">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-473">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="55338-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="55338-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="55338-475">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-475">Add a folder named *Models*.</span></span>

* <span data-ttu-id="55338-476">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-476">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="55338-477">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="55338-478">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-478">Right-click the project.</span></span> <span data-ttu-id="55338-479">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-479">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="55338-480">フォルダーに「*Models*」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="55338-480">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="55338-482">*Models* フォルダーを右クリックし、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-482">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="55338-483">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-483">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="55338-484">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-484">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="55338-485">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="55338-485">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="55338-486">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="55338-486">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="55338-487">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="55338-487">Add a database context</span></span>

<span data-ttu-id="55338-488">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="55338-488">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="55338-489">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-489">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-490">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-490">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55338-491">*Models* フォルダーを右クリックし、 **[追加]**  >  **[クラス]** の順にクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-491">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="55338-492">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-492">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="55338-493">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="55338-494">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-494">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="55338-495">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="55338-496">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="55338-496">Register the database context</span></span>

<span data-ttu-id="55338-497">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="55338-497">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="55338-498">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="55338-498">The container provides the service to controllers.</span></span>

<span data-ttu-id="55338-499">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="55338-499">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="55338-500">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="55338-500">The preceding code:</span></span>

* <span data-ttu-id="55338-501">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="55338-501">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="55338-502">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-502">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="55338-503">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="55338-503">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="55338-504">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="55338-504">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-505">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-505">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55338-506">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="55338-506">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="55338-507">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-507">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="55338-508">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-508">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="55338-509">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-509">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="55338-511">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-511">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="55338-512">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-512">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="55338-513">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-513">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="55338-514">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="55338-514">The preceding code:</span></span>

* <span data-ttu-id="55338-515">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="55338-515">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="55338-516">クラスを [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="55338-516">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="55338-517">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="55338-517">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="55338-518">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-518">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="55338-519">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="55338-519">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="55338-520">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="55338-520">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="55338-521">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="55338-521">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="55338-522">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="55338-522">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="55338-523">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-523">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="55338-524">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="55338-524">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="55338-525">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="55338-525">Add Get methods</span></span>

<span data-ttu-id="55338-526">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-526">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="55338-527">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="55338-527">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="55338-528">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="55338-528">Stop the app if it's still running.</span></span> <span data-ttu-id="55338-529">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="55338-529">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="55338-530">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="55338-530">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="55338-531">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="55338-531">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="55338-532">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-532">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="55338-533">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="55338-533">Routing and URL paths</span></span>

<span data-ttu-id="55338-534">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="55338-534">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="55338-535">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-535">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="55338-536">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="55338-536">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="55338-537">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-537">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="55338-538">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="55338-538">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="55338-539">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="55338-539">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="55338-540">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-540">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="55338-541">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="55338-541">This sample doesn't use a template.</span></span> <span data-ttu-id="55338-542">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-542">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="55338-543">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="55338-543">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="55338-544">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="55338-544">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="55338-545">戻り値</span><span class="sxs-lookup"><span data-stu-id="55338-545">Return values</span></span>

<span data-ttu-id="55338-546">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="55338-546">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="55338-547">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="55338-547">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="55338-548">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="55338-548">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="55338-549">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="55338-549">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="55338-550">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="55338-550">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="55338-551">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="55338-551">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="55338-552">要求された ID と一致するアイテムがない場合、メソッドは 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="55338-552">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="55338-553">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="55338-553">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="55338-554">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-554">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="55338-555">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="55338-555">Test the GetTodoItems method</span></span>

<span data-ttu-id="55338-556">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="55338-556">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="55338-557">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="55338-557">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="55338-558">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="55338-558">Start the web app.</span></span>
* <span data-ttu-id="55338-559">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="55338-559">Start Postman.</span></span>
* <span data-ttu-id="55338-560">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="55338-560">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="55338-561">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="55338-561">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="55338-562">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="55338-562">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="55338-563">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="55338-563">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="55338-564">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="55338-564">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="55338-565">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="55338-565">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="55338-566">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="55338-566">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="55338-567">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-567">Create a new request.</span></span>
  * <span data-ttu-id="55338-568">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-568">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="55338-569">要求 URL を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-569">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="55338-570">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="55338-570">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="55338-571">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-571">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="55338-572">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-572">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="55338-574">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="55338-574">Add a Create method</span></span>

<span data-ttu-id="55338-575">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-575">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="55338-576">[`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="55338-576">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="55338-577">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="55338-577">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="55338-578">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="55338-578">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="55338-579">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-579">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="55338-580">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="55338-580">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="55338-581">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-581">Adds a `Location` header to the response.</span></span> <span data-ttu-id="55338-582">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="55338-582">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="55338-583">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-583">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="55338-584">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-584">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="55338-585">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="55338-585">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="55338-586">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="55338-586">Test the PostTodoItem method</span></span>

* <span data-ttu-id="55338-587">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="55338-587">Build the project.</span></span>
* <span data-ttu-id="55338-588">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-588">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="55338-589">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-589">Select the **Body** tab.</span></span>
* <span data-ttu-id="55338-590">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-590">Select the **raw** radio button.</span></span>
* <span data-ttu-id="55338-591">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-591">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="55338-592">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="55338-592">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="55338-593">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-593">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="55338-595">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="55338-595">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="55338-596">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="55338-596">Test the location header URI</span></span>

* <span data-ttu-id="55338-597">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-597">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="55338-598">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="55338-598">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="55338-600">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-600">Set the method to GET.</span></span>
* <span data-ttu-id="55338-601">URI を貼り付けます (たとえば、`https://localhost:5001/api/Todo/2`)。</span><span class="sxs-lookup"><span data-stu-id="55338-601">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="55338-602">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-602">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="55338-603">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="55338-603">Add a PutTodoItem method</span></span>

<span data-ttu-id="55338-604">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-604">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="55338-605">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="55338-605">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="55338-606">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="55338-606">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="55338-607">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="55338-607">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="55338-608">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="55338-608">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="55338-609">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="55338-609">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="55338-610">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="55338-610">Test the PutTodoItem method</span></span>

<span data-ttu-id="55338-611">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="55338-611">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="55338-612">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="55338-612">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="55338-613">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="55338-613">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="55338-614">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-614">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="55338-615">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="55338-615">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="55338-617">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="55338-617">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="55338-618">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-618">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="55338-619">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="55338-619">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="55338-620">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="55338-620">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="55338-621">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="55338-621">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="55338-622">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-622">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="55338-623">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="55338-623">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="55338-624">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="55338-624">Select **Send**.</span></span>

<span data-ttu-id="55338-625">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="55338-625">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="55338-626">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="55338-626">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="55338-627">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="55338-627">Call the web API with JavaScript</span></span>

<span data-ttu-id="55338-628">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-628">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="55338-629">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="55338-629">jQuery initiates the request.</span></span> <span data-ttu-id="55338-630">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="55338-630">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="55338-631">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)し、[既定のファイル マッピングを有効にする](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="55338-631">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="55338-632">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="55338-632">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="55338-633">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-633">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="55338-634">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-634">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="55338-635">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="55338-635">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="55338-636">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="55338-636">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="55338-637">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="55338-637">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="55338-638">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="55338-638">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="55338-639">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="55338-639">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="55338-640">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="55338-640">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="55338-641">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="55338-641">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="55338-642">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="55338-642">Get a list of to-do items</span></span>

<span data-ttu-id="55338-643">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="55338-643">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="55338-644">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="55338-644">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="55338-645">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="55338-645">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="55338-646">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="55338-646">Add a to-do item</span></span>

<span data-ttu-id="55338-647">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="55338-647">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="55338-648">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="55338-648">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="55338-649">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="55338-649">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="55338-650">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="55338-650">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="55338-651">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="55338-651">Update a to-do item</span></span>

<span data-ttu-id="55338-652">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="55338-652">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="55338-653">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="55338-653">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="55338-654">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="55338-654">Delete a to-do item</span></span>

<span data-ttu-id="55338-655">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="55338-655">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="55338-656">Web API に認証サポートを追加</span><span class="sxs-lookup"><span data-stu-id="55338-656">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="additional-resources"></a>[!INCLUDE[](~/includes/Identity<span data-ttu-id="55338-657">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="55338-657">Server4.md)]</span></span>

<span data-ttu-id="55338-658">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="55338-658">Additional resources</span></span> <span data-ttu-id="55338-659">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="55338-659">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span>

<span data-ttu-id="55338-660">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-660">See [how to download](xref:index#how-to-download-a-sample).</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* <span data-ttu-id="55338-661">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="55338-661">For more information, see the following resources:</span></span>
