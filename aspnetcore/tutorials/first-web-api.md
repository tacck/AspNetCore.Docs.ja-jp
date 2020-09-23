---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ebce9f2f4992d83c6b28edb5c771cdfc8a7a0b6a
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080382"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="99b1d-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="99b1d-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="99b1d-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5)、[Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="99b1d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="99b1d-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="99b1d-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="99b1d-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-107">Create a web API project.</span></span>
> * <span data-ttu-id="99b1d-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="99b1d-109">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="99b1d-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="99b1d-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="99b1d-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="99b1d-111">Call the web API with Postman.</span></span>

<span data-ttu-id="99b1d-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="99b1d-113">概要</span><span class="sxs-lookup"><span data-stu-id="99b1d-113">Overview</span></span>

<span data-ttu-id="99b1d-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="99b1d-115">API</span><span class="sxs-lookup"><span data-stu-id="99b1d-115">API</span></span> | <span data-ttu-id="99b1d-116">説明</span><span class="sxs-lookup"><span data-stu-id="99b1d-116">Description</span></span> | <span data-ttu-id="99b1d-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="99b1d-117">Request body</span></span> | <span data-ttu-id="99b1d-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="99b1d-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="99b1d-119">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-119">Get all to-do items</span></span> | <span data-ttu-id="99b1d-120">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-120">None</span></span> | <span data-ttu-id="99b1d-121">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="99b1d-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="99b1d-122">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-122">Get an item by ID</span></span> | <span data-ttu-id="99b1d-123">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-123">None</span></span> | <span data-ttu-id="99b1d-124">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="99b1d-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="99b1d-125">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-125">Add a new item</span></span> | <span data-ttu-id="99b1d-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="99b1d-126">To-do item</span></span> | <span data-ttu-id="99b1d-127">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="99b1d-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="99b1d-128">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="99b1d-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="99b1d-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="99b1d-129">To-do item</span></span> | <span data-ttu-id="99b1d-130">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-130">None</span></span> |
|<span data-ttu-id="99b1d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="99b1d-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="99b1d-132">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="99b1d-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="99b1d-133">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-133">None</span></span> | <span data-ttu-id="99b1d-134">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-134">None</span></span>|

<span data-ttu-id="99b1d-135">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-135">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="99b1d-141">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="99b1d-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="99b1d-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99b1d-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="99b1d-145">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="99b1d-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="99b1d-147">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="99b1d-148">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="99b1d-149">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="99b1d-150">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="99b1d-151">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-151">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="99b1d-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99b1d-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="99b1d-154">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="99b1d-155">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="99b1d-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="99b1d-157">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="99b1d-158">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-158">The preceding commands:</span></span>

  * <span data-ttu-id="99b1d-159">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="99b1d-160">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="99b1d-162">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-162">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="99b1d-164">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="99b1d-165">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS での API テンプレートの選択](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="99b1d-167">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 3.x **ターゲット フレームワーク**を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="99b1d-168">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-168">Select **Next**.</span></span>

* <span data-ttu-id="99b1d-169">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="99b1d-171">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="99b1d-172">API のテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-172">Test the API</span></span>

<span data-ttu-id="99b1d-173">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-173">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="99b1d-174">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-174">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-175">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99b1d-176">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-176">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="99b1d-177">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-177">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="99b1d-178">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-178">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="99b1d-179">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-179">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99b1d-180">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99b1d-180">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="99b1d-181">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="99b1d-182">ブラウザーで、次の URL に移動します: `https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="99b1d-182">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-183">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-183">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="99b1d-184">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-184">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="99b1d-185">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-185">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="99b1d-186">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-186">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="99b1d-187">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="99b1d-187">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="99b1d-188">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-188">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="99b1d-189">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-189">Add a model class</span></span>

<span data-ttu-id="99b1d-190">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-190">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="99b1d-191">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-191">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="99b1d-193">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-193">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="99b1d-194">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-194">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="99b1d-195">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-195">Name the folder *Models*.</span></span>

* <span data-ttu-id="99b1d-196">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-196">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="99b1d-197">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-197">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="99b1d-198">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-198">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99b1d-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99b1d-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="99b1d-200">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-200">Add a folder named *Models*.</span></span>

* <span data-ttu-id="99b1d-201">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-201">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="99b1d-203">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-203">Right-click the project.</span></span> <span data-ttu-id="99b1d-204">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-204">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="99b1d-205">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-205">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="99b1d-207">*Models* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-207">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="99b1d-208">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-208">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="99b1d-209">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-209">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="99b1d-210">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-210">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="99b1d-211">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-211">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="99b1d-212">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-212">Add a database context</span></span>

<span data-ttu-id="99b1d-213">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-213">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="99b1d-214">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-214">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-215">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-215">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="99b1d-216">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="99b1d-216">Add NuGet packages</span></span>

* <span data-ttu-id="99b1d-217">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-217">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="99b1d-218">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-218">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="99b1d-219">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-219">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="99b1d-220">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-220">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="99b1d-221">前の手順を使用して、**Microsoft.EntityFrameworkCore.InMemory** NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-221">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="99b1d-223">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-223">Add the TodoContext database context</span></span>

* <span data-ttu-id="99b1d-224">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-224">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="99b1d-225">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-225">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-226">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-226">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="99b1d-227">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-227">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="99b1d-228">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-228">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="99b1d-229">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="99b1d-229">Register the database context</span></span>

<span data-ttu-id="99b1d-230">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-230">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="99b1d-231">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-231">The container provides the service to controllers.</span></span>

<span data-ttu-id="99b1d-232">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-232">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="99b1d-233">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-233">The preceding code:</span></span>

* <span data-ttu-id="99b1d-234">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-234">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="99b1d-235">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-235">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="99b1d-236">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-236">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="99b1d-237">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="99b1d-237">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-238">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-238">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="99b1d-239">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-239">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="99b1d-240">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-240">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="99b1d-241">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-241">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="99b1d-242">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-242">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="99b1d-243">**モデル クラス**で **TodoItem (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-243">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="99b1d-244">**データ コンテキスト クラス**で **TodoContext (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-244">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="99b1d-245">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-245">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-246">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-246">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="99b1d-247">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-247">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="99b1d-248">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-248">The preceding commands:</span></span>

* <span data-ttu-id="99b1d-249">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-249">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="99b1d-250">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-250">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="99b1d-251">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-251">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="99b1d-252">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-252">The generated code:</span></span>

* <span data-ttu-id="99b1d-253">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-253">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="99b1d-254">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-254">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="99b1d-255">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-255">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="99b1d-256">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-256">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="99b1d-257">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-257">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="99b1d-258">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-258">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="99b1d-259">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-259">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="99b1d-260">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="99b1d-260">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="99b1d-261">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-261">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="99b1d-262">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="99b1d-262">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="99b1d-263">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="99b1d-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="99b1d-264">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="99b1d-265">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-265">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="99b1d-266">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="99b1d-267">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-267">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="99b1d-268"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="99b1d-268">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="99b1d-269">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-269">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="99b1d-270">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="99b1d-270">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="99b1d-271">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-271">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="99b1d-272">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-272">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="99b1d-273">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-273">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="99b1d-274">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-274">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="99b1d-275">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-275">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="99b1d-276">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="99b1d-276">Install Postman</span></span>

<span data-ttu-id="99b1d-277">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-277">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="99b1d-278">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-278">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="99b1d-279">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-279">Start the web app.</span></span>
* <span data-ttu-id="99b1d-280">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-280">Start Postman.</span></span>
* <span data-ttu-id="99b1d-281">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-281">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="99b1d-282">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-282">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="99b1d-283">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-283">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="99b1d-284">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-284">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="99b1d-285">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-285">Create a new request.</span></span>
* <span data-ttu-id="99b1d-286">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-286">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="99b1d-287">URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-287">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="99b1d-288">たとえば、「 `https://localhost:5001/api/TodoItems` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-288">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="99b1d-289">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-289">Select the **Body** tab.</span></span>
* <span data-ttu-id="99b1d-290">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-290">Select the **raw** radio button.</span></span>
* <span data-ttu-id="99b1d-291">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-291">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="99b1d-292">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-292">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="99b1d-293">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-293">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="99b1d-295">Postman で Location ヘッダーの URI をテストする</span><span class="sxs-lookup"><span data-stu-id="99b1d-295">Test the location header URI with Postman</span></span>

* <span data-ttu-id="99b1d-296">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-296">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="99b1d-297">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-297">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="99b1d-299">HTTP メソッドを `GET` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-299">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="99b1d-300">URI を `https://localhost:<port>/api/TodoItems/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-300">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="99b1d-301">たとえば、「 `https://localhost:5001/api/TodoItems/1` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-301">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="99b1d-302">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-302">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="99b1d-303">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="99b1d-303">Examine the GET methods</span></span>

<span data-ttu-id="99b1d-304">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-304">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="99b1d-305">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-305">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="99b1d-306">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-306">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="99b1d-307">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-307">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="99b1d-308">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="99b1d-308">Test Get with Postman</span></span>

* <span data-ttu-id="99b1d-309">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-309">Create a new request.</span></span>
* <span data-ttu-id="99b1d-310">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-310">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="99b1d-311">要求 URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-311">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="99b1d-312">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-312">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="99b1d-313">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-313">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="99b1d-314">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-314">Select **Send**.</span></span>

<span data-ttu-id="99b1d-315">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-315">This app uses an in-memory database.</span></span> <span data-ttu-id="99b1d-316">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="99b1d-316">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="99b1d-317">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-317">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="99b1d-318">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="99b1d-318">Routing and URL paths</span></span>

<span data-ttu-id="99b1d-319">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-319">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="99b1d-320">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-320">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="99b1d-321">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-321">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="99b1d-322">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-322">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="99b1d-323">このサンプルでは、コントローラー クラス名は **TodoItems**Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="99b1d-323">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="99b1d-324">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="99b1d-324">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="99b1d-325">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-325">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="99b1d-326">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="99b1d-326">This sample doesn't use a template.</span></span> <span data-ttu-id="99b1d-327">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-327">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="99b1d-328">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="99b1d-328">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="99b1d-329">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-329">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="99b1d-330">戻り値</span><span class="sxs-lookup"><span data-stu-id="99b1d-330">Return values</span></span>

<span data-ttu-id="99b1d-331">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="99b1d-331">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="99b1d-332">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-332">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="99b1d-333">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-333">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="99b1d-334">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-334">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="99b1d-335">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-335">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="99b1d-336">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-336">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="99b1d-337">要求された ID と一致するアイテムがない場合、メソッドは 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-337">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="99b1d-338">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-338">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="99b1d-339">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-339">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="99b1d-340">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="99b1d-340">The PutTodoItem method</span></span>

<span data-ttu-id="99b1d-341">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-341">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="99b1d-342">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-342">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="99b1d-343">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-343">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="99b1d-344">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-344">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="99b1d-345">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-345">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="99b1d-346">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-346">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="99b1d-347">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-347">Test the PutTodoItem method</span></span>

<span data-ttu-id="99b1d-348">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-348">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="99b1d-349">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-349">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="99b1d-350">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-350">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="99b1d-351">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-351">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="99b1d-352">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-352">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="99b1d-354">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="99b1d-354">The DeleteTodoItem method</span></span>

<span data-ttu-id="99b1d-355">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-355">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="99b1d-356">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-356">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="99b1d-357">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-357">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="99b1d-358">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-358">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="99b1d-359">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-359">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="99b1d-360">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-360">Select **Send**.</span></span>

<a name="over-post"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="99b1d-361">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="99b1d-361">Prevent over-posting</span></span>

<span data-ttu-id="99b1d-362">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-362">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="99b1d-363">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-363">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="99b1d-364">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-364">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="99b1d-365">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-365">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="99b1d-366">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-366">**DTO** is used in this article.</span></span>

<span data-ttu-id="99b1d-367">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-367">A DTO may be used to:</span></span>

* <span data-ttu-id="99b1d-368">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-368">Prevent over-posting.</span></span>
* <span data-ttu-id="99b1d-369">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="99b1d-369">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="99b1d-370">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-370">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="99b1d-371">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-371">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="99b1d-372">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-372">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="99b1d-373">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-373">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="99b1d-374">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-374">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="99b1d-375">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-375">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="99b1d-376">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-376">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="99b1d-377">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-377">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="99b1d-378">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-378">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="99b1d-379">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="99b1d-379">Call the web API with JavaScript</span></span>

<span data-ttu-id="99b1d-380">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-380">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="99b1d-381">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-381">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="99b1d-382">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-382">Create a web API project.</span></span>
> * <span data-ttu-id="99b1d-383">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-383">Add a model class and a database context.</span></span>
> * <span data-ttu-id="99b1d-384">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-384">Add a controller.</span></span>
> * <span data-ttu-id="99b1d-385">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-385">Add CRUD methods.</span></span>
> * <span data-ttu-id="99b1d-386">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-386">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="99b1d-387">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="99b1d-387">Specify return values.</span></span>
> * <span data-ttu-id="99b1d-388">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="99b1d-388">Call the web API with Postman.</span></span>
> * <span data-ttu-id="99b1d-389">JavaScript を使用した Web API の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="99b1d-389">Call the web API with JavaScript.</span></span>

<span data-ttu-id="99b1d-390">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-390">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="99b1d-391">概要</span><span class="sxs-lookup"><span data-stu-id="99b1d-391">Overview</span></span>

<span data-ttu-id="99b1d-392">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-392">This tutorial creates the following API:</span></span>

|<span data-ttu-id="99b1d-393">API</span><span class="sxs-lookup"><span data-stu-id="99b1d-393">API</span></span> | <span data-ttu-id="99b1d-394">説明</span><span class="sxs-lookup"><span data-stu-id="99b1d-394">Description</span></span> | <span data-ttu-id="99b1d-395">要求本文</span><span class="sxs-lookup"><span data-stu-id="99b1d-395">Request body</span></span> | <span data-ttu-id="99b1d-396">応答本文</span><span class="sxs-lookup"><span data-stu-id="99b1d-396">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="99b1d-397">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="99b1d-397">GET /api/TodoItems</span></span> | <span data-ttu-id="99b1d-398">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-398">Get all to-do items</span></span> | <span data-ttu-id="99b1d-399">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-399">None</span></span> | <span data-ttu-id="99b1d-400">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="99b1d-400">Array of to-do items</span></span>|
|<span data-ttu-id="99b1d-401">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="99b1d-401">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="99b1d-402">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-402">Get an item by ID</span></span> | <span data-ttu-id="99b1d-403">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-403">None</span></span> | <span data-ttu-id="99b1d-404">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="99b1d-404">To-do item</span></span>|
|<span data-ttu-id="99b1d-405">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="99b1d-405">POST /api/TodoItems</span></span> | <span data-ttu-id="99b1d-406">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-406">Add a new item</span></span> | <span data-ttu-id="99b1d-407">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="99b1d-407">To-do item</span></span> | <span data-ttu-id="99b1d-408">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="99b1d-408">To-do item</span></span> |
|<span data-ttu-id="99b1d-409">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="99b1d-409">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="99b1d-410">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="99b1d-410">Update an existing item &nbsp;</span></span> | <span data-ttu-id="99b1d-411">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="99b1d-411">To-do item</span></span> | <span data-ttu-id="99b1d-412">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-412">None</span></span> |
|<span data-ttu-id="99b1d-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="99b1d-413">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="99b1d-414">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="99b1d-414">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="99b1d-415">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-415">None</span></span> | <span data-ttu-id="99b1d-416">None</span><span class="sxs-lookup"><span data-stu-id="99b1d-416">None</span></span>|

<span data-ttu-id="99b1d-417">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-417">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="99b1d-423">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="99b1d-423">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-424">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-424">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="99b1d-425">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99b1d-425">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-426">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-426">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="99b1d-427">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="99b1d-427">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-428">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="99b1d-429">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-429">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="99b1d-430">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-430">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="99b1d-431">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-431">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="99b1d-432">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-432">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="99b1d-433">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-433">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="99b1d-434">**[Enable Docker Support]\(Docker サポートを有効にする\)** は**選択しないで**ください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-434">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="99b1d-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99b1d-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="99b1d-437">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-437">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="99b1d-438">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-438">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="99b1d-439">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-439">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="99b1d-440">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-440">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="99b1d-441">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-441">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-442">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="99b1d-443">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-443">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="99b1d-445">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-445">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="99b1d-446">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-446">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="99b1d-447">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 2.x **ターゲット フレームワーク**を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-447">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="99b1d-448">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-448">Select **Next**.</span></span>

* <span data-ttu-id="99b1d-449">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-449">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="99b1d-451">API のテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-451">Test the API</span></span>

<span data-ttu-id="99b1d-452">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-452">The project template creates a `values` API.</span></span> <span data-ttu-id="99b1d-453">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-453">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-454">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-454">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="99b1d-455">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-455">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="99b1d-456">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-456">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="99b1d-457">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-457">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="99b1d-458">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-458">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99b1d-459">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99b1d-459">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="99b1d-460">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-460">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="99b1d-461">ブラウザーで、次の URL に移動します: `https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="99b1d-461">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-462">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-462">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="99b1d-463">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-463">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="99b1d-464">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-464">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="99b1d-465">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-465">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="99b1d-466">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="99b1d-466">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="99b1d-467">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-467">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="99b1d-468">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-468">Add a model class</span></span>

<span data-ttu-id="99b1d-469">*モデル*は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-469">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="99b1d-470">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-470">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="99b1d-472">**ソリューション エクスプローラー**で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-472">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="99b1d-473">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-473">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="99b1d-474">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-474">Name the folder *Models*.</span></span>

* <span data-ttu-id="99b1d-475">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-475">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="99b1d-476">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-476">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="99b1d-477">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-477">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99b1d-478">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99b1d-478">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="99b1d-479">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-479">Add a folder named *Models*.</span></span>

* <span data-ttu-id="99b1d-480">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-480">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-481">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-481">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="99b1d-482">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-482">Right-click the project.</span></span> <span data-ttu-id="99b1d-483">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-483">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="99b1d-484">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-484">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="99b1d-486">*Models* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-486">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="99b1d-487">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-487">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="99b1d-488">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-488">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="99b1d-489">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-489">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="99b1d-490">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-490">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="99b1d-491">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-491">Add a database context</span></span>

<span data-ttu-id="99b1d-492">*データベース コンテキスト*は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-492">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="99b1d-493">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-493">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-494">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-494">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="99b1d-495">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-495">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="99b1d-496">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-496">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-497">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-497">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="99b1d-498">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-498">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="99b1d-499">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-499">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="99b1d-500">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="99b1d-500">Register the database context</span></span>

<span data-ttu-id="99b1d-501">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-501">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="99b1d-502">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-502">The container provides the service to controllers.</span></span>

<span data-ttu-id="99b1d-503">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-503">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="99b1d-504">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-504">The preceding code:</span></span>

* <span data-ttu-id="99b1d-505">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-505">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="99b1d-506">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-506">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="99b1d-507">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-507">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="99b1d-508">コントローラーの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-508">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-509">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="99b1d-510">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-510">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="99b1d-511">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-511">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="99b1d-512">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-512">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="99b1d-513">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-513">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-515">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-515">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="99b1d-516">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-516">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="99b1d-517">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-517">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="99b1d-518">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-518">The preceding code:</span></span>

* <span data-ttu-id="99b1d-519">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-519">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="99b1d-520">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-520">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="99b1d-521">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-521">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="99b1d-522">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-522">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="99b1d-523">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-523">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="99b1d-524">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-524">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="99b1d-525">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-525">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="99b1d-526">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-526">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="99b1d-527">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-527">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="99b1d-528">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-528">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="99b1d-529">Get メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-529">Add Get methods</span></span>

<span data-ttu-id="99b1d-530">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-530">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="99b1d-531">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-531">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="99b1d-532">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-532">Stop the app if it's still running.</span></span> <span data-ttu-id="99b1d-533">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-533">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="99b1d-534">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-534">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="99b1d-535">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-535">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="99b1d-536">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-536">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="99b1d-537">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="99b1d-537">Routing and URL paths</span></span>

<span data-ttu-id="99b1d-538">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-538">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="99b1d-539">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-539">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="99b1d-540">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-540">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="99b1d-541">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-541">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="99b1d-542">このサンプルでは、コントローラー クラス名は **Todo**Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="99b1d-542">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="99b1d-543">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="99b1d-543">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="99b1d-544">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-544">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="99b1d-545">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="99b1d-545">This sample doesn't use a template.</span></span> <span data-ttu-id="99b1d-546">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-546">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="99b1d-547">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="99b1d-547">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="99b1d-548">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-548">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="99b1d-549">戻り値</span><span class="sxs-lookup"><span data-stu-id="99b1d-549">Return values</span></span>

<span data-ttu-id="99b1d-550">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="99b1d-550">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="99b1d-551">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-551">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="99b1d-552">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-552">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="99b1d-553">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-553">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="99b1d-554">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-554">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="99b1d-555">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-555">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="99b1d-556">要求された ID と一致するアイテムがない場合、メソッドは 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-556">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="99b1d-557">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-557">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="99b1d-558">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-558">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="99b1d-559">GetTodoItems メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-559">Test the GetTodoItems method</span></span>

<span data-ttu-id="99b1d-560">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-560">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="99b1d-561">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-561">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="99b1d-562">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-562">Start the web app.</span></span>
* <span data-ttu-id="99b1d-563">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-563">Start Postman.</span></span>
* <span data-ttu-id="99b1d-564">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-564">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99b1d-565">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99b1d-565">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="99b1d-566">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-566">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="99b1d-567">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99b1d-567">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="99b1d-568">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証**を無効にします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-568">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="99b1d-569">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-569">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="99b1d-570">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-570">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="99b1d-571">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-571">Create a new request.</span></span>
  * <span data-ttu-id="99b1d-572">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-572">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="99b1d-573">要求 URI を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-573">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="99b1d-574">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-574">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="99b1d-575">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-575">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="99b1d-576">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-576">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="99b1d-578">Create メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-578">Add a Create method</span></span>

<span data-ttu-id="99b1d-579">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-579">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="99b1d-580">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-580">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="99b1d-581">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-581">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="99b1d-582">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="99b1d-582">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="99b1d-583">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-583">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="99b1d-584">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="99b1d-584">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="99b1d-585">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-585">Adds a `Location` header to the response.</span></span> <span data-ttu-id="99b1d-586">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-586">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="99b1d-587">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-587">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="99b1d-588">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-588">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="99b1d-589">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-589">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="99b1d-590">PostTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-590">Test the PostTodoItem method</span></span>

* <span data-ttu-id="99b1d-591">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-591">Build the project.</span></span>
* <span data-ttu-id="99b1d-592">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-592">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="99b1d-593">URI を `https://localhost:<port>/api/TodoItem` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-593">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="99b1d-594">たとえば、「 `https://localhost:5001/api/TodoItem` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-594">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="99b1d-595">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-595">Select the **Body** tab.</span></span>
* <span data-ttu-id="99b1d-596">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-596">Select the **raw** radio button.</span></span>
* <span data-ttu-id="99b1d-597">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-597">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="99b1d-598">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-598">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="99b1d-599">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-599">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="99b1d-601">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-601">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="99b1d-602">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-602">Test the location header URI</span></span>

* <span data-ttu-id="99b1d-603">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-603">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="99b1d-604">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="99b1d-604">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="99b1d-606">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-606">Set the method to GET.</span></span>
<span data-ttu-id="99b1d-607">\* URI を  `https://localhost:<port>/api/TodoItems/2` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-607">\* Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span><span data-ttu-id="99b1d-608"> たとえば、 `https://localhost:5001/api/TodoItems/2` などです。</span><span class="sxs-lookup"><span data-stu-id="99b1d-608"> For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="99b1d-609">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-609">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="99b1d-610">PutTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-610">Add a PutTodoItem method</span></span>

<span data-ttu-id="99b1d-611">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-611">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="99b1d-612">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-612">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="99b1d-613">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-613">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="99b1d-614">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-614">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="99b1d-615">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-615">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="99b1d-616">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-616">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="99b1d-617">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-617">Test the PutTodoItem method</span></span>

<span data-ttu-id="99b1d-618">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-618">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="99b1d-619">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-619">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="99b1d-620">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-620">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="99b1d-621">ID = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-621">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="99b1d-622">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-622">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="99b1d-624">DeleteTodoItem メソッドの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-624">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="99b1d-625">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-625">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="99b1d-626">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-626">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="99b1d-627">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="99b1d-627">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="99b1d-628">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-628">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="99b1d-629">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-629">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="99b1d-630">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-630">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="99b1d-631">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-631">Select **Send**.</span></span>

<span data-ttu-id="99b1d-632">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-632">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="99b1d-633">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-633">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="99b1d-634">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="99b1d-634">Call the web API with JavaScript</span></span>

<span data-ttu-id="99b1d-635">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-635">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="99b1d-636">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-636">jQuery initiates the request.</span></span> <span data-ttu-id="99b1d-637">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-637">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="99b1d-638">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)し、[既定のファイル マッピングを有効にする](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-638">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="99b1d-639">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-639">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="99b1d-640">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-640">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="99b1d-641">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-641">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="99b1d-642">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-642">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="99b1d-643">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-643">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="99b1d-644">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-644">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="99b1d-645">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-645">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="99b1d-646">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-646">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="99b1d-647">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-647">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="99b1d-648">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-648">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="99b1d-649">To Do アイテムのリストの取得</span><span class="sxs-lookup"><span data-stu-id="99b1d-649">Get a list of to-do items</span></span>

<span data-ttu-id="99b1d-650">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-650">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="99b1d-651">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-651">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="99b1d-652">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-652">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="99b1d-653">To Do アイテムの追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-653">Add a to-do item</span></span>

<span data-ttu-id="99b1d-654">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-654">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="99b1d-655">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-655">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="99b1d-656">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="99b1d-656">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="99b1d-657">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-657">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="99b1d-658">To Do アイテムの更新</span><span class="sxs-lookup"><span data-stu-id="99b1d-658">Update a to-do item</span></span>

<span data-ttu-id="99b1d-659">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="99b1d-659">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="99b1d-660">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="99b1d-660">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="99b1d-661">To Do アイテムの削除</span><span class="sxs-lookup"><span data-stu-id="99b1d-661">Delete a to-do item</span></span>

<span data-ttu-id="99b1d-662">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-662">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="99b1d-663">Web API に認証サポートを追加</span><span class="sxs-lookup"><span data-stu-id="99b1d-663">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="99b1d-664">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="99b1d-664">Additional resources</span></span>

<span data-ttu-id="99b1d-665">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="99b1d-665">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="99b1d-666">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-666">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="99b1d-667">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="99b1d-667">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="99b1d-668">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="99b1d-668">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
