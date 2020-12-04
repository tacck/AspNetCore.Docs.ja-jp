---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: ccbfc27eb89e23938a69f0ab4cb306d6a4136889
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96175053"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="282af-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="282af-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="282af-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5)、[Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="282af-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="282af-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="282af-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="282af-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="282af-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="282af-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="282af-107">Create a web API project.</span></span>
> * <span data-ttu-id="282af-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="282af-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="282af-109">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="282af-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="282af-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="282af-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="282af-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="282af-111">Call the web API with Postman.</span></span>

<span data-ttu-id="282af-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="282af-113">概要</span><span class="sxs-lookup"><span data-stu-id="282af-113">Overview</span></span>

<span data-ttu-id="282af-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="282af-115">API</span><span class="sxs-lookup"><span data-stu-id="282af-115">API</span></span> | <span data-ttu-id="282af-116">説明</span><span class="sxs-lookup"><span data-stu-id="282af-116">Description</span></span> | <span data-ttu-id="282af-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="282af-117">Request body</span></span> | <span data-ttu-id="282af-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="282af-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="282af-119">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-119">Get all to-do items</span></span> | <span data-ttu-id="282af-120">None</span><span class="sxs-lookup"><span data-stu-id="282af-120">None</span></span> | <span data-ttu-id="282af-121">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="282af-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="282af-122">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-122">Get an item by ID</span></span> | <span data-ttu-id="282af-123">None</span><span class="sxs-lookup"><span data-stu-id="282af-123">None</span></span> | <span data-ttu-id="282af-124">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="282af-125">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-125">Add a new item</span></span> | <span data-ttu-id="282af-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-126">To-do item</span></span> | <span data-ttu-id="282af-127">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="282af-128">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="282af-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-129">To-do item</span></span> | <span data-ttu-id="282af-130">None</span><span class="sxs-lookup"><span data-stu-id="282af-130">None</span></span> |
|<span data-ttu-id="282af-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="282af-132">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="282af-133">None</span><span class="sxs-lookup"><span data-stu-id="282af-133">None</span></span> | <span data-ttu-id="282af-134">None</span><span class="sxs-lookup"><span data-stu-id="282af-134">None</span></span>|

<span data-ttu-id="282af-135">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="282af-135">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="282af-141">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="282af-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="282af-145">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="282af-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-147">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="282af-148">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-148">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="282af-149">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="282af-150">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 5.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="282af-151">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-151">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="282af-154">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="282af-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="282af-155">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="282af-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="282af-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="282af-157">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="282af-158">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-158">The preceding commands:</span></span>

  * <span data-ttu-id="282af-159">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="282af-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="282af-160">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="282af-162">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-162">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="282af-164">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="282af-165">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS での API テンプレートの選択](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="282af-167">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 5.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="282af-168">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-168">Select **Next**.</span></span>

* <span data-ttu-id="282af-169">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="282af-171">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="282af-172">プロジェクトをテストする</span><span class="sxs-lookup"><span data-stu-id="282af-172">Test the project</span></span>

<span data-ttu-id="282af-173">プロジェクト テンプレートにより、[Swagger](xref:tutorials/web-api-help-pages-using-swagger) をサポートする `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="282af-175">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="282af-176">Visual Studio により、以下が起動されます。</span><span class="sxs-lookup"><span data-stu-id="282af-176">Visual Studio launches:</span></span>

* <span data-ttu-id="282af-177">IIS Express Web サーバー。</span><span class="sxs-lookup"><span data-stu-id="282af-177">The IIS Express web server.</span></span>
* <span data-ttu-id="282af-178">既定のブラウザーです。`https://localhost:<port>/swagger/index.html` に移動します。`<port>` はランダムに選択されるポート番号です。</span><span class="sxs-lookup"><span data-stu-id="282af-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="282af-180">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="282af-181">ブラウザーで、次の URL に移動します: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="282af-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="282af-183">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="282af-184">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="282af-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="282af-185">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="282af-186">URL に `/swagger` を追加します (URL を `https://localhost:<port>/swagger` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="282af-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="282af-187">Swagger ページ `/swagger/index.html` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="282af-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="282af-188">**[取得]**  >  **[試してみる]**  >  **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="282af-189">ページに以下が表示されます。</span><span class="sxs-lookup"><span data-stu-id="282af-189">The page displays:</span></span>

* <span data-ttu-id="282af-190">WeatherForecast API をテストするための [Curl](https://curl.haxx.se/) コマンド。</span><span class="sxs-lookup"><span data-stu-id="282af-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="282af-191">WeatherForecast API をテストする URL。</span><span class="sxs-lookup"><span data-stu-id="282af-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="282af-192">応答コード、本文、およびヘッダー。</span><span class="sxs-lookup"><span data-stu-id="282af-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="282af-193">メディアの種類と、値とスキーマの例を含むドロップ ダウン リスト ボックス。</span><span class="sxs-lookup"><span data-stu-id="282af-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="282af-194">Swagger は、Web API の有用なドキュメントやヘルプ ページを生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="282af-195">このチュートリアルでは、Web API の作成について説明します。</span><span class="sxs-lookup"><span data-stu-id="282af-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="282af-196">Swagger の詳細については、<xref:tutorials/web-api-help-pages-using-swagger> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="282af-197">ブラウザーで **要求 URL** をコピーして貼り付けます: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="282af-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="282af-198">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="282af-199">launchUrl を更新する</span><span class="sxs-lookup"><span data-stu-id="282af-199">Update the launchUrl</span></span>

<span data-ttu-id="282af-200">*Properties\launchSettings.json* で、`launchUrl` を `"swagger"` から `"api/TodoItems"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="282af-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="282af-201">Swagger が削除されているため、上記のマークアップにより、開始される URL が、次のセクションで追加されるコントローラーの GET メソッドに変更されます。</span><span class="sxs-lookup"><span data-stu-id="282af-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="282af-202">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="282af-202">Add a model class</span></span>

<span data-ttu-id="282af-203">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="282af-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="282af-204">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="282af-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-206">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="282af-207">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="282af-208">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="282af-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="282af-209">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="282af-210">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="282af-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="282af-213">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="282af-214">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="282af-216">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-216">Right-click the project.</span></span> <span data-ttu-id="282af-217">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="282af-218">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="282af-218">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="282af-220">*Models* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="282af-221">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="282af-222">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="282af-223">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="282af-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="282af-224">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="282af-225">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="282af-225">Add a database context</span></span>

<span data-ttu-id="282af-226">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="282af-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="282af-227">このクラスは <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="282af-229">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="282af-229">Add NuGet packages</span></span>

* <span data-ttu-id="282af-230">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="282af-231">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-231">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="282af-232">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-232">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="282af-233">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="282af-234">前の手順を使用して、**Microsoft.EntityFrameworkCore.InMemory** NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-234">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="282af-235">![NuGet パッケージ マネージャー](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="282af-235">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="282af-236">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="282af-236">Add the TodoContext database context</span></span>

* <span data-ttu-id="282af-237">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-237">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="282af-238">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-238">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="282af-239">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="282af-240">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-240">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="282af-241">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-241">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="282af-242">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="282af-242">Register the database context</span></span>

<span data-ttu-id="282af-243">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="282af-243">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="282af-244">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="282af-244">The container provides the service to controllers.</span></span>

<span data-ttu-id="282af-245">次のコードを使用して *Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="282af-245">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="282af-246">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-246">The preceding code:</span></span>

* <span data-ttu-id="282af-247">Swagger 呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="282af-247">Removes the Swagger calls.</span></span>
* <span data-ttu-id="282af-248">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="282af-248">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="282af-249">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-249">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="282af-250">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="282af-250">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="282af-251">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="282af-251">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-252">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-253">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-253">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="282af-254">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-254">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="282af-255">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-255">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="282af-256">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-256">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="282af-257">**モデル クラス** で **TodoItem (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-257">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="282af-258">**データ コンテキスト クラス** で **TodoContext (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-258">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="282af-259">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="282af-259">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="282af-260">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-260">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="282af-261">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-261">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet tool update -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="282af-262">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-262">The preceding commands:</span></span>

* <span data-ttu-id="282af-263">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-263">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="282af-264">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="282af-264">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="282af-265">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="282af-265">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="282af-266">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-266">The generated code:</span></span>

* <span data-ttu-id="282af-267">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="282af-267">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="282af-268">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="282af-268">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="282af-269">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-269">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="282af-270">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="282af-270">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="282af-271">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-271">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="282af-272">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="282af-272">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="282af-273">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="282af-273">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="282af-274">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="282af-274">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="282af-275">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="282af-275">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="282af-276">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="282af-276">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="282af-277">PostTodoItem 作成メソッドの更新</span><span class="sxs-lookup"><span data-stu-id="282af-277">Update the PostTodoItem create method</span></span>

<span data-ttu-id="282af-278">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-278">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="282af-279">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="282af-279">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="282af-280">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-280">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="282af-281">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-281">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="282af-282"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="282af-282">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="282af-283">成功すると、[HTTP 201 状態コード](https://developer.mozilla.org/docs/Web/HTTP/Status/201)が返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-283">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="282af-284">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="282af-284">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="282af-285">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="282af-285">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="282af-286">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="282af-286">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="282af-287">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-287">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="282af-288">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-288">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="282af-289">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="282af-289">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="282af-290">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="282af-290">Install Postman</span></span>

<span data-ttu-id="282af-291">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="282af-291">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="282af-292">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="282af-292">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="282af-293">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-293">Start the web app.</span></span>
* <span data-ttu-id="282af-294">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-294">Start Postman.</span></span>
* <span data-ttu-id="282af-295">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-295">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="282af-296">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-296">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="282af-297">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-297">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="282af-298">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-298">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="282af-299">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-299">Create a new request.</span></span>
* <span data-ttu-id="282af-300">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-300">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="282af-301">URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-301">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="282af-302">たとえば、「 `https://localhost:5001/api/TodoItems` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-302">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="282af-303">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-303">Select the **Body** tab.</span></span>
* <span data-ttu-id="282af-304">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-304">Select the **raw** radio button.</span></span>
* <span data-ttu-id="282af-305">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-305">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="282af-306">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-306">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="282af-307">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-307">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="282af-309">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-309">Test the location header URI</span></span>

<span data-ttu-id="282af-310">場所ヘッダー URI は、ブラウザーでテストできます。</span><span class="sxs-lookup"><span data-stu-id="282af-310">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="282af-311">場所ヘッダー URI をコピーしてブラウザーに貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="282af-311">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="282af-312">Postman でテストするには:</span><span class="sxs-lookup"><span data-stu-id="282af-312">To test in Postman:</span></span>

* <span data-ttu-id="282af-313">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-313">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="282af-314">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="282af-314">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="282af-316">HTTP メソッドを `GET` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-316">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="282af-317">URI を `https://localhost:<port>/api/TodoItems/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-317">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="282af-318">たとえば、「 `https://localhost:5001/api/TodoItems/1` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-318">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="282af-319">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-319">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="282af-320">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="282af-320">Examine the GET methods</span></span>

<span data-ttu-id="282af-321">2 つの GET エンドポイントが実装されます。</span><span class="sxs-lookup"><span data-stu-id="282af-321">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="282af-322">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="282af-322">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="282af-323">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="282af-323">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="282af-324">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-324">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="282af-325">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="282af-325">Test Get with Postman</span></span>

* <span data-ttu-id="282af-326">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-326">Create a new request.</span></span>
* <span data-ttu-id="282af-327">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-327">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="282af-328">要求 URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-328">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="282af-329">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="282af-329">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="282af-330">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-330">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="282af-331">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-331">Select **Send**.</span></span>

<span data-ttu-id="282af-332">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-332">This app uses an in-memory database.</span></span> <span data-ttu-id="282af-333">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="282af-333">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="282af-334">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="282af-334">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="282af-335">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="282af-335">Routing and URL paths</span></span>

<span data-ttu-id="282af-336">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="282af-336">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="282af-337">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-337">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="282af-338">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-338">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="282af-339">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-339">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="282af-340">このサンプルでは、コントローラー クラス名は **TodoItems** Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="282af-340">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="282af-341">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="282af-341">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="282af-342">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-342">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="282af-343">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="282af-343">This sample doesn't use a template.</span></span> <span data-ttu-id="282af-344">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-344">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="282af-345">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="282af-345">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="282af-346">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="282af-346">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="282af-347">戻り値</span><span class="sxs-lookup"><span data-stu-id="282af-347">Return values</span></span>

<span data-ttu-id="282af-348">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="282af-348">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="282af-349">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="282af-349">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="282af-350">ハンドルされない例外がないと仮定すると、この戻り値の型の応答コードは [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200) です。</span><span class="sxs-lookup"><span data-stu-id="282af-350">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="282af-351">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="282af-351">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="282af-352">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="282af-352">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="282af-353">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="282af-353">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="282af-354">要求された ID に一致するアイテムがない場合、このメソッドにより [404 ステータス](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-354">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="282af-355">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="282af-355">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="282af-356">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-356">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="282af-357">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="282af-357">The PutTodoItem method</span></span>

<span data-ttu-id="282af-358">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="282af-358">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="282af-359">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-359">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="282af-360">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="282af-360">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="282af-361">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="282af-361">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="282af-362">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-362">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="282af-363">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="282af-363">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="282af-364">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="282af-364">Test the PutTodoItem method</span></span>

<span data-ttu-id="282af-365">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="282af-365">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="282af-366">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="282af-366">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="282af-367">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-367">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="282af-368">Id = 1 の To Do アイテムを更新し、その名前を `"feed fish"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-368">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="282af-369">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="282af-369">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="282af-371">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="282af-371">The DeleteTodoItem method</span></span>

<span data-ttu-id="282af-372">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="282af-372">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="282af-373">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="282af-373">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="282af-374">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="282af-374">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="282af-375">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-375">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="282af-376">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-376">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="282af-377">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-377">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="282af-378">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="282af-378">Prevent over-posting</span></span>

<span data-ttu-id="282af-379">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="282af-379">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="282af-380">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="282af-380">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="282af-381">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="282af-381">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="282af-382">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="282af-382">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="282af-383">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="282af-383">**DTO** is used in this article.</span></span>

<span data-ttu-id="282af-384">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="282af-384">A DTO may be used to:</span></span>

* <span data-ttu-id="282af-385">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="282af-385">Prevent over-posting.</span></span>
* <span data-ttu-id="282af-386">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="282af-386">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="282af-387">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="282af-387">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="282af-388">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="282af-388">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="282af-389">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="282af-389">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="282af-390">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="282af-390">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="282af-391">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="282af-391">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="282af-392">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-392">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="282af-393">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-393">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="282af-394">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="282af-394">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="282af-395">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-395">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="282af-396">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="282af-396">Call the web API with JavaScript</span></span>

<span data-ttu-id="282af-397">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-397">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="282af-398">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="282af-398">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="282af-399">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="282af-399">Create a web API project.</span></span>
> * <span data-ttu-id="282af-400">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="282af-400">Add a model class and a database context.</span></span>
> * <span data-ttu-id="282af-401">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="282af-401">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="282af-402">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="282af-402">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="282af-403">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="282af-403">Call the web API with Postman.</span></span>

<span data-ttu-id="282af-404">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-404">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="282af-405">概要</span><span class="sxs-lookup"><span data-stu-id="282af-405">Overview</span></span>

<span data-ttu-id="282af-406">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-406">This tutorial creates the following API:</span></span>

|<span data-ttu-id="282af-407">API</span><span class="sxs-lookup"><span data-stu-id="282af-407">API</span></span> | <span data-ttu-id="282af-408">説明</span><span class="sxs-lookup"><span data-stu-id="282af-408">Description</span></span> | <span data-ttu-id="282af-409">要求本文</span><span class="sxs-lookup"><span data-stu-id="282af-409">Request body</span></span> | <span data-ttu-id="282af-410">応答本文</span><span class="sxs-lookup"><span data-stu-id="282af-410">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="282af-411">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-411">Get all to-do items</span></span> | <span data-ttu-id="282af-412">None</span><span class="sxs-lookup"><span data-stu-id="282af-412">None</span></span> | <span data-ttu-id="282af-413">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="282af-413">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="282af-414">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-414">Get an item by ID</span></span> | <span data-ttu-id="282af-415">None</span><span class="sxs-lookup"><span data-stu-id="282af-415">None</span></span> | <span data-ttu-id="282af-416">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-416">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="282af-417">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-417">Add a new item</span></span> | <span data-ttu-id="282af-418">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-418">To-do item</span></span> | <span data-ttu-id="282af-419">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-419">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="282af-420">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-420">Update an existing item &nbsp;</span></span> | <span data-ttu-id="282af-421">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-421">To-do item</span></span> | <span data-ttu-id="282af-422">None</span><span class="sxs-lookup"><span data-stu-id="282af-422">None</span></span> |
|<span data-ttu-id="282af-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-423">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="282af-424">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-424">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="282af-425">None</span><span class="sxs-lookup"><span data-stu-id="282af-425">None</span></span> | <span data-ttu-id="282af-426">None</span><span class="sxs-lookup"><span data-stu-id="282af-426">None</span></span>|

<span data-ttu-id="282af-427">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="282af-427">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="282af-433">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="282af-433">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-436">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="282af-437">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="282af-437">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-438">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-439">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-439">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="282af-440">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-440">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="282af-441">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-441">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="282af-442">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-442">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="282af-443">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-443">Select the **API** template and click **Create**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="282af-446">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="282af-446">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="282af-447">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="282af-447">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="282af-448">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-448">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="282af-449">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-449">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="282af-450">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-450">The preceding commands:</span></span>

  * <span data-ttu-id="282af-451">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="282af-451">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="282af-452">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-452">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-453">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-453">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="282af-454">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-454">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="282af-456">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-456">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="282af-457">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-457">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![macOS での API テンプレートの選択](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="282af-459">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 3.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-459">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="282af-460">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-460">Select **Next**.</span></span>

* <span data-ttu-id="282af-461">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-461">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="282af-463">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-463">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="282af-464">API のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-464">Test the API</span></span>

<span data-ttu-id="282af-465">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-465">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="282af-466">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="282af-466">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="282af-468">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-468">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="282af-469">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="282af-469">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="282af-470">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-470">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="282af-471">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-471">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-472">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-472">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="282af-473">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-473">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="282af-474">ブラウザーで、次の URL に移動します: `https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="282af-474">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-475">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-475">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="282af-476">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-476">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="282af-477">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="282af-477">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="282af-478">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-478">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="282af-479">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="282af-479">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="282af-480">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-480">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="282af-481">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="282af-481">Add a model class</span></span>

<span data-ttu-id="282af-482">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="282af-482">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="282af-483">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="282af-483">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-484">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-484">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-485">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-485">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="282af-486">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-486">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="282af-487">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="282af-487">Name the folder *Models*.</span></span>

* <span data-ttu-id="282af-488">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-488">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="282af-489">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-489">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="282af-490">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-490">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-491">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-491">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="282af-492">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-492">Add a folder named *Models*.</span></span>

* <span data-ttu-id="282af-493">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-493">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-494">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-494">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="282af-495">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-495">Right-click the project.</span></span> <span data-ttu-id="282af-496">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-496">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="282af-497">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="282af-497">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="282af-499">*Models* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-499">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="282af-500">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-500">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="282af-501">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-501">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="282af-502">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="282af-502">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="282af-503">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-503">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="282af-504">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="282af-504">Add a database context</span></span>

<span data-ttu-id="282af-505">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="282af-505">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="282af-506">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-506">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-507">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-507">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="282af-508">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="282af-508">Add NuGet packages</span></span>

* <span data-ttu-id="282af-509">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-509">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="282af-510">**[参照]** タブを選択し、検索ボックスに「**Microsoft.EntityFrameworkCore.SqlServer**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-510">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="282af-511">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-511">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="282af-512">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-512">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="282af-513">前の手順を使用して、**Microsoft.EntityFrameworkCore.InMemory** NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-513">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="282af-515">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="282af-515">Add the TodoContext database context</span></span>

* <span data-ttu-id="282af-516">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-516">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="282af-517">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-517">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="282af-518">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-518">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="282af-519">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-519">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="282af-520">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-520">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="282af-521">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="282af-521">Register the database context</span></span>

<span data-ttu-id="282af-522">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="282af-522">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="282af-523">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="282af-523">The container provides the service to controllers.</span></span>

<span data-ttu-id="282af-524">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="282af-524">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="282af-525">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-525">The preceding code:</span></span>

* <span data-ttu-id="282af-526">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="282af-526">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="282af-527">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-527">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="282af-528">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="282af-528">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="282af-529">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="282af-529">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-530">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-530">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-531">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-531">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="282af-532">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-532">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="282af-533">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-533">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="282af-534">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-534">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="282af-535">**モデル クラス** で **TodoItem (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-535">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="282af-536">**データ コンテキスト クラス** で **TodoContext (TodoApi.Models)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-536">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="282af-537">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="282af-537">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="282af-538">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-538">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="282af-539">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-539">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="282af-540">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-540">The preceding commands:</span></span>

* <span data-ttu-id="282af-541">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-541">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="282af-542">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="282af-542">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="282af-543">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="282af-543">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="282af-544">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-544">The generated code:</span></span>

* <span data-ttu-id="282af-545">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="282af-545">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="282af-546">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="282af-546">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="282af-547">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-547">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="282af-548">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="282af-548">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="282af-549">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-549">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="282af-550">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="282af-550">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="282af-551">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="282af-551">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="282af-552">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="282af-552">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="282af-553">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="282af-553">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="282af-554">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="282af-554">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="282af-555">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="282af-555">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="282af-556">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-556">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="282af-557">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="282af-557">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="282af-558">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-558">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="282af-559">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-559">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="282af-560"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="282af-560">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="282af-561">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-561">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="282af-562">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="282af-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="282af-563">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="282af-563">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="282af-564">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="282af-564">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="282af-565">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="282af-566">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="282af-567">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="282af-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="282af-568">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="282af-568">Install Postman</span></span>

<span data-ttu-id="282af-569">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="282af-569">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="282af-570">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="282af-570">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="282af-571">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-571">Start the web app.</span></span>
* <span data-ttu-id="282af-572">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-572">Start Postman.</span></span>
* <span data-ttu-id="282af-573">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-573">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="282af-574">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-574">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="282af-575">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-575">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="282af-576">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-576">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="282af-577">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-577">Create a new request.</span></span>
* <span data-ttu-id="282af-578">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-578">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="282af-579">URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-579">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="282af-580">たとえば、「 `https://localhost:5001/api/TodoItems` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-580">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="282af-581">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-581">Select the **Body** tab.</span></span>
* <span data-ttu-id="282af-582">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-582">Select the **raw** radio button.</span></span>
* <span data-ttu-id="282af-583">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-583">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="282af-584">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-584">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="282af-585">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-585">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="282af-587">Postman で Location ヘッダーの URI をテストする</span><span class="sxs-lookup"><span data-stu-id="282af-587">Test the location header URI with Postman</span></span>

* <span data-ttu-id="282af-588">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-588">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="282af-589">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="282af-589">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="282af-591">HTTP メソッドを `GET` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-591">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="282af-592">URI を `https://localhost:<port>/api/TodoItems/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-592">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="282af-593">たとえば、「 `https://localhost:5001/api/TodoItems/1` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-593">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="282af-594">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-594">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="282af-595">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="282af-595">Examine the GET methods</span></span>

<span data-ttu-id="282af-596">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="282af-596">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="282af-597">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="282af-597">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="282af-598">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="282af-598">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="282af-599">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-599">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="282af-600">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="282af-600">Test Get with Postman</span></span>

* <span data-ttu-id="282af-601">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-601">Create a new request.</span></span>
* <span data-ttu-id="282af-602">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-602">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="282af-603">要求 URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-603">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="282af-604">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="282af-604">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="282af-605">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-605">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="282af-606">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-606">Select **Send**.</span></span>

<span data-ttu-id="282af-607">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-607">This app uses an in-memory database.</span></span> <span data-ttu-id="282af-608">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="282af-608">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="282af-609">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="282af-609">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="282af-610">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="282af-610">Routing and URL paths</span></span>

<span data-ttu-id="282af-611">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="282af-611">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="282af-612">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-612">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="282af-613">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-613">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="282af-614">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-614">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="282af-615">このサンプルでは、コントローラー クラス名は **TodoItems** Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="282af-615">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="282af-616">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="282af-616">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="282af-617">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-617">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="282af-618">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="282af-618">This sample doesn't use a template.</span></span> <span data-ttu-id="282af-619">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-619">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="282af-620">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="282af-620">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="282af-621">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="282af-621">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="282af-622">戻り値</span><span class="sxs-lookup"><span data-stu-id="282af-622">Return values</span></span> 

<span data-ttu-id="282af-623">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="282af-623">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="282af-624">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="282af-624">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="282af-625">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="282af-625">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="282af-626">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="282af-626">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="282af-627">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="282af-627">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="282af-628">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="282af-628">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="282af-629">要求された ID と一致するアイテムがない場合、メソッドは 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="282af-629">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="282af-630">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="282af-630">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="282af-631">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-631">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="282af-632">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="282af-632">The PutTodoItem method</span></span>

<span data-ttu-id="282af-633">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="282af-633">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="282af-634">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-634">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="282af-635">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="282af-635">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="282af-636">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="282af-636">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="282af-637">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-637">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="282af-638">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="282af-638">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="282af-639">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="282af-639">Test the PutTodoItem method</span></span>

<span data-ttu-id="282af-640">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="282af-640">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="282af-641">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="282af-641">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="282af-642">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-642">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="282af-643">Id = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-643">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="282af-644">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="282af-644">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="282af-646">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="282af-646">The DeleteTodoItem method</span></span>

<span data-ttu-id="282af-647">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="282af-647">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="282af-648">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="282af-648">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="282af-649">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="282af-649">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="282af-650">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-650">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="282af-651">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-651">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="282af-652">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-652">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="282af-653">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="282af-653">Prevent over-posting</span></span>

<span data-ttu-id="282af-654">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="282af-654">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="282af-655">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="282af-655">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="282af-656">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="282af-656">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="282af-657">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="282af-657">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="282af-658">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="282af-658">**DTO** is used in this article.</span></span>

<span data-ttu-id="282af-659">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="282af-659">A DTO may be used to:</span></span>

* <span data-ttu-id="282af-660">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="282af-660">Prevent over-posting.</span></span>
* <span data-ttu-id="282af-661">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="282af-661">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="282af-662">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="282af-662">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="282af-663">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="282af-663">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="282af-664">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="282af-664">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="282af-665">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="282af-665">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="282af-666">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="282af-666">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="282af-667">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-667">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="282af-668">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-668">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="282af-669">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="282af-669">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="282af-670">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-670">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="282af-671">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="282af-671">Call the web API with JavaScript</span></span>

<span data-ttu-id="282af-672">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-672">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="282af-673">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="282af-673">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="282af-674">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="282af-674">Create a web API project.</span></span>
> * <span data-ttu-id="282af-675">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="282af-675">Add a model class and a database context.</span></span>
> * <span data-ttu-id="282af-676">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="282af-676">Add a controller.</span></span>
> * <span data-ttu-id="282af-677">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="282af-677">Add CRUD methods.</span></span>
> * <span data-ttu-id="282af-678">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="282af-678">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="282af-679">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="282af-679">Specify return values.</span></span>
> * <span data-ttu-id="282af-680">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="282af-680">Call the web API with Postman.</span></span>
> * <span data-ttu-id="282af-681">JavaScript を使用した Web API の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="282af-681">Call the web API with JavaScript.</span></span>

<span data-ttu-id="282af-682">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-682">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="282af-683">概要 2.1</span><span class="sxs-lookup"><span data-stu-id="282af-683">Overview 2.1</span></span>

<span data-ttu-id="282af-684">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-684">This tutorial creates the following API:</span></span>

|<span data-ttu-id="282af-685">API</span><span class="sxs-lookup"><span data-stu-id="282af-685">API</span></span> | <span data-ttu-id="282af-686">説明</span><span class="sxs-lookup"><span data-stu-id="282af-686">Description</span></span> | <span data-ttu-id="282af-687">要求本文</span><span class="sxs-lookup"><span data-stu-id="282af-687">Request body</span></span> | <span data-ttu-id="282af-688">応答本文</span><span class="sxs-lookup"><span data-stu-id="282af-688">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="282af-689">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="282af-689">GET /api/TodoItems</span></span> | <span data-ttu-id="282af-690">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-690">Get all to-do items</span></span> | <span data-ttu-id="282af-691">None</span><span class="sxs-lookup"><span data-stu-id="282af-691">None</span></span> | <span data-ttu-id="282af-692">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="282af-692">Array of to-do items</span></span>|
|<span data-ttu-id="282af-693">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="282af-693">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="282af-694">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-694">Get an item by ID</span></span> | <span data-ttu-id="282af-695">None</span><span class="sxs-lookup"><span data-stu-id="282af-695">None</span></span> | <span data-ttu-id="282af-696">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-696">To-do item</span></span>|
|<span data-ttu-id="282af-697">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="282af-697">POST /api/TodoItems</span></span> | <span data-ttu-id="282af-698">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-698">Add a new item</span></span> | <span data-ttu-id="282af-699">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-699">To-do item</span></span> | <span data-ttu-id="282af-700">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-700">To-do item</span></span> |
|<span data-ttu-id="282af-701">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="282af-701">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="282af-702">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-702">Update an existing item &nbsp;</span></span> | <span data-ttu-id="282af-703">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="282af-703">To-do item</span></span> | <span data-ttu-id="282af-704">None</span><span class="sxs-lookup"><span data-stu-id="282af-704">None</span></span> |
|<span data-ttu-id="282af-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-705">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="282af-706">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="282af-706">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="282af-707">None</span><span class="sxs-lookup"><span data-stu-id="282af-707">None</span></span> | <span data-ttu-id="282af-708">None</span><span class="sxs-lookup"><span data-stu-id="282af-708">None</span></span>|

<span data-ttu-id="282af-709">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="282af-709">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="282af-715">前提条件 2.1</span><span class="sxs-lookup"><span data-stu-id="282af-715">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-716">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-716">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-717">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-717">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-718">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-718">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="282af-719">Web プロジェクト 2.1 の作成</span><span class="sxs-lookup"><span data-stu-id="282af-719">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-720">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-720">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-721">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-721">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="282af-722">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-722">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="282af-723">プロジェクトに「*TodoApi*」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-723">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="282af-724">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-724">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="282af-725">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-725">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="282af-726">**[Enable Docker Support]\(Docker サポートを有効にする\)** は **選択しないで** ください。</span><span class="sxs-lookup"><span data-stu-id="282af-726">**Don't** select **Enable Docker Support**.</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-728">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-728">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="282af-729">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="282af-729">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="282af-730">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="282af-730">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="282af-731">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-731">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="282af-732">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="282af-732">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="282af-733">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-733">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-734">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-734">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="282af-735">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-735">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="282af-737">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-737">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="282af-738">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-738">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="282af-739">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 2.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-739">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="282af-740">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-740">Select **Next**.</span></span>

* <span data-ttu-id="282af-741">**[プロジェクト名]** に「*TodoApi*」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-741">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="282af-743">API 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-743">Test the API 2.1</span></span>

<span data-ttu-id="282af-744">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-744">The project template creates a `values` API.</span></span> <span data-ttu-id="282af-745">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="282af-745">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-746">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-746">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="282af-747">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-747">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="282af-748">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="282af-748">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="282af-749">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-749">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="282af-750">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-750">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-751">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-751">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="282af-752">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="282af-752">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="282af-753">ブラウザーで、次の URL に移動します: `https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="282af-753">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-754">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-754">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="282af-755">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-755">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="282af-756">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="282af-756">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="282af-757">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-757">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="282af-758">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="282af-758">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="282af-759">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-759">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="282af-760">モデル クラス 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="282af-760">Add a model class 2.1</span></span>

<span data-ttu-id="282af-761">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="282af-761">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="282af-762">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="282af-762">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-763">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-763">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-764">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-764">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="282af-765">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-765">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="282af-766">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="282af-766">Name the folder *Models*.</span></span>

* <span data-ttu-id="282af-767">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-767">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="282af-768">クラスに「*TodoItem*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-768">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="282af-769">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-769">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="282af-770">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="282af-770">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="282af-771">*Models* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-771">Add a folder named *Models*.</span></span>

* <span data-ttu-id="282af-772">次のコードを使用して、`TodoItem` クラスを *Models* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-772">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="282af-773">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-773">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="282af-774">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-774">Right-click the project.</span></span> <span data-ttu-id="282af-775">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-775">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="282af-776">フォルダーに「 *Models* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="282af-776">Name the folder *Models*.</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="282af-778">*Models* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-778">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="282af-779">クラスに「*TodoItem*」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-779">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="282af-780">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-780">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="282af-781">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="282af-781">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="282af-782">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *Models* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-782">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="282af-783">データベース コンテキスト 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="282af-783">Add a database context 2.1</span></span>

<span data-ttu-id="282af-784">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="282af-784">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="282af-785">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-785">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-786">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-786">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-787">*Models* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-787">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="282af-788">クラスに「*TodoContext*」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-788">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="282af-789">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-789">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="282af-790">*Models* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-790">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="282af-791">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-791">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="282af-792">データベース コンテキスト 2.1 の登録</span><span class="sxs-lookup"><span data-stu-id="282af-792">Register the database context 2.1</span></span>

<span data-ttu-id="282af-793">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="282af-793">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="282af-794">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="282af-794">The container provides the service to controllers.</span></span>

<span data-ttu-id="282af-795">次の強調表示されているコードを使用して、*Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="282af-795">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="282af-796">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-796">The preceding code:</span></span>

* <span data-ttu-id="282af-797">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="282af-797">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="282af-798">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-798">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="282af-799">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="282af-799">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="282af-800">コントローラー 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="282af-800">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-801">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-801">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-802">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="282af-802">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="282af-803">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-803">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="282af-804">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-804">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="282af-805">クラスに「*TodoController*」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-805">Name the class *TodoController*, and select **Add**.</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="282af-807">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-807">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="282af-808">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-808">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="282af-809">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-809">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="282af-810">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="282af-810">The preceding code:</span></span>

* <span data-ttu-id="282af-811">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="282af-811">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="282af-812">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="282af-812">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="282af-813">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="282af-813">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="282af-814">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-814">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="282af-815">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="282af-815">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="282af-816">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="282af-816">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="282af-817">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="282af-817">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="282af-818">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="282af-818">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="282af-819">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-819">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="282af-820">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="282af-820">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="282af-821">Get メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="282af-821">Add Get methods 2.1</span></span>

<span data-ttu-id="282af-822">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-822">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="282af-823">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="282af-823">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="282af-824">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="282af-824">Stop the app if it's still running.</span></span> <span data-ttu-id="282af-825">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="282af-825">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="282af-826">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="282af-826">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="282af-827">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="282af-827">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="282af-828">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-828">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="282af-829">ルーティングと URL パス 2.1</span><span class="sxs-lookup"><span data-stu-id="282af-829">Routing and URL paths 2.1</span></span>

<span data-ttu-id="282af-830">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="282af-830">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="282af-831">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-831">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="282af-832">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-832">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="282af-833">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-833">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="282af-834">このサンプルでは、コントローラー クラス名は **Todo** Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="282af-834">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="282af-835">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="282af-835">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="282af-836">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-836">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="282af-837">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="282af-837">This sample doesn't use a template.</span></span> <span data-ttu-id="282af-838">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-838">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="282af-839">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="282af-839">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="282af-840">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="282af-840">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="282af-841">戻り値 2.1</span><span class="sxs-lookup"><span data-stu-id="282af-841">Return values 2.1</span></span>

<span data-ttu-id="282af-842">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="282af-842">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="282af-843">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="282af-843">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="282af-844">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="282af-844">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="282af-845">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="282af-845">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="282af-846">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="282af-846">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="282af-847">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="282af-847">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="282af-848">要求された ID と一致するアイテムがない場合、メソッドは 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="282af-848">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="282af-849">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="282af-849">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="282af-850">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-850">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="282af-851">GetTodoItems メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-851">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="282af-852">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="282af-852">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="282af-853">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="282af-853">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="282af-854">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-854">Start the web app.</span></span>
* <span data-ttu-id="282af-855">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="282af-855">Start Postman.</span></span>
* <span data-ttu-id="282af-856">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-856">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="282af-857">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="282af-857">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="282af-858">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-858">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="282af-859">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="282af-859">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="282af-860">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、**SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-860">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="282af-861">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-861">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="282af-862">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="282af-862">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="282af-863">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-863">Create a new request.</span></span>
  * <span data-ttu-id="282af-864">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-864">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="282af-865">要求 URI を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-865">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="282af-866">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="282af-866">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="282af-867">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-867">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="282af-868">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-868">Select **Send**.</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="282af-870">Create メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="282af-870">Add a Create method 2.1</span></span>

<span data-ttu-id="282af-871">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-871">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="282af-872">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="282af-872">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="282af-873">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="282af-873">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="282af-874">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="282af-874">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="282af-875">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-875">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="282af-876">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="282af-876">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="282af-877">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-877">Adds a `Location` header to the response.</span></span> <span data-ttu-id="282af-878">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="282af-878">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="282af-879">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-879">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="282af-880">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-880">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="282af-881">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="282af-881">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="282af-882">PostTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-882">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="282af-883">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="282af-883">Build the project.</span></span>
* <span data-ttu-id="282af-884">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-884">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="282af-885">URI を `https://localhost:<port>/api/Todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-885">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="282af-886">たとえば、「 `https://localhost:5001/api/Todo` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-886">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="282af-887">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-887">Select the **Body** tab.</span></span>
* <span data-ttu-id="282af-888">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-888">Select the **raw** radio button.</span></span>
* <span data-ttu-id="282af-889">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-889">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="282af-890">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-890">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="282af-891">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-891">Select **Send**.</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="282af-893">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="282af-893">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="282af-894">場所ヘッダー URI 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-894">Test the location header URI 2.1</span></span>

* <span data-ttu-id="282af-895">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-895">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="282af-896">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="282af-896">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="282af-898">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-898">Set the method to GET.</span></span>
* <span data-ttu-id="282af-899">URI を `https://localhost:<port>/api/TodoItems/2` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-899">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="282af-900">たとえば、「 `https://localhost:5001/api/TodoItems/2` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="282af-900">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="282af-901">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-901">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="282af-902">PutTodoItem メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="282af-902">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="282af-903">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-903">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="282af-904">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-904">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="282af-905">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="282af-905">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="282af-906">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="282af-906">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="282af-907">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="282af-907">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="282af-908">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="282af-908">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="282af-909">PutTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-909">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="282af-910">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="282af-910">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="282af-911">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="282af-911">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="282af-912">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="282af-912">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="282af-913">Id = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-913">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="282af-914">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="282af-914">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="282af-916">DeleteTodoItem メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="282af-916">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="282af-917">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-917">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="282af-918">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="282af-918">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="282af-919">DeleteTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="282af-919">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="282af-920">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="282af-920">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="282af-921">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-921">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="282af-922">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="282af-922">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="282af-923">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="282af-923">Select **Send**.</span></span>

<span data-ttu-id="282af-924">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="282af-924">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="282af-925">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="282af-925">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="282af-926">JavaScript 2.1 を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="282af-926">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="282af-927">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-927">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="282af-928">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="282af-928">jQuery initiates the request.</span></span> <span data-ttu-id="282af-929">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="282af-929">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="282af-930">*Startup.cs* を次の強調表示されたコードで更新して、[静的ファイルを提供](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)し、[既定のファイル マッピングを有効にする](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="282af-930">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="282af-931">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="282af-931">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="282af-932">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-932">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="282af-933">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-933">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="282af-934">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="282af-934">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="282af-935">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="282af-935">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="282af-936">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="282af-936">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="282af-937">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="282af-937">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="282af-938">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="282af-938">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="282af-939">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="282af-939">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="282af-940">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="282af-940">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="282af-941">To Do アイテム 2.1 のリストの取得</span><span class="sxs-lookup"><span data-stu-id="282af-941">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="282af-942">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="282af-942">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="282af-943">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="282af-943">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="282af-944">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="282af-944">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="282af-945">To Do アイテム 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="282af-945">Add a to-do item 2.1</span></span>

<span data-ttu-id="282af-946">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="282af-946">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="282af-947">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="282af-947">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="282af-948">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="282af-948">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="282af-949">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="282af-949">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="282af-950">To Do アイテム 2.1 の更新</span><span class="sxs-lookup"><span data-stu-id="282af-950">Update a to-do item 2.1</span></span>

<span data-ttu-id="282af-951">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="282af-951">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="282af-952">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="282af-952">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="282af-953">To Do アイテム 2.1 の削除</span><span class="sxs-lookup"><span data-stu-id="282af-953">Delete a to-do item 2.1</span></span>

<span data-ttu-id="282af-954">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="282af-954">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="282af-955">Web API 2.1 への認証サポートの追加</span><span class="sxs-lookup"><span data-stu-id="282af-955">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="282af-956">その他のリソース 2.1</span><span class="sxs-lookup"><span data-stu-id="282af-956">Additional resources 2.1</span></span>

<span data-ttu-id="282af-957">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="282af-957">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="282af-958">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-958">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="282af-959">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="282af-959">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="282af-960">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="282af-960">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
