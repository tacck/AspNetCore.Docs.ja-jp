---
title: 'チュートリアル: ASP.NET Core で Web API を作成する'
author: rick-anderson
description: ASP.NET Core で Web API をビルドする方法を学習します。
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/13/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
- ':::no-loc(Models):::'
uid: tutorials/first-web-api
ms.openlocfilehash: 17f04dc9a0bdcf8ff016d83b915c017ff485cb36
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690706"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="2a97c-103">チュートリアル: ASP.NET Core で Web API を作成する</span><span class="sxs-lookup"><span data-stu-id="2a97c-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="2a97c-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)、[Kirk Larkin](https://twitter.com/serpent5)、[Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="2a97c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="2a97c-105">このチュートリアルでは、ASP.NET Core で Web API をビルドする方法の基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2a97c-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a97c-107">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-107">Create a web API project.</span></span>
> * <span data-ttu-id="2a97c-108">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="2a97c-109">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="2a97c-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="2a97c-110">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="2a97c-111">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="2a97c-111">Call the web API with Postman.</span></span>

<span data-ttu-id="2a97c-112">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="2a97c-113">概要</span><span class="sxs-lookup"><span data-stu-id="2a97c-113">Overview</span></span>

<span data-ttu-id="2a97c-114">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="2a97c-115">API</span><span class="sxs-lookup"><span data-stu-id="2a97c-115">API</span></span> | <span data-ttu-id="2a97c-116">説明</span><span class="sxs-lookup"><span data-stu-id="2a97c-116">Description</span></span> | <span data-ttu-id="2a97c-117">要求本文</span><span class="sxs-lookup"><span data-stu-id="2a97c-117">Request body</span></span> | <span data-ttu-id="2a97c-118">応答本文</span><span class="sxs-lookup"><span data-stu-id="2a97c-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="2a97c-119">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-119">Get all to-do items</span></span> | <span data-ttu-id="2a97c-120">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-120">None</span></span> | <span data-ttu-id="2a97c-121">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="2a97c-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="2a97c-122">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-122">Get an item by ID</span></span> | <span data-ttu-id="2a97c-123">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-123">None</span></span> | <span data-ttu-id="2a97c-124">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="2a97c-125">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-125">Add a new item</span></span> | <span data-ttu-id="2a97c-126">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-126">To-do item</span></span> | <span data-ttu-id="2a97c-127">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="2a97c-128">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="2a97c-129">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-129">To-do item</span></span> | <span data-ttu-id="2a97c-130">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-130">None</span></span> |
|<span data-ttu-id="2a97c-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a97c-132">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a97c-133">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-133">None</span></span> | <span data-ttu-id="2a97c-134">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-134">None</span></span>|

<span data-ttu-id="2a97c-135">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-135">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="2a97c-141">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2a97c-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-144">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="2a97c-145">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="2a97c-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-147">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-147">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="2a97c-148">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-148">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="2a97c-149">プロジェクトに「 *TodoApi* 」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-149">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="2a97c-150">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 5.0]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="2a97c-151">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-151">Select the **API** template and click **Create** .</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a97c-154">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2a97c-155">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="2a97c-156">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="2a97c-157">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-157">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

  <span data-ttu-id="2a97c-158">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-158">The preceding commands:</span></span>

  * <span data-ttu-id="2a97c-159">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="2a97c-160">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-161">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a97c-162">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-162">Select **File** > **New Solution** .</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="2a97c-164">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="2a97c-165">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![macOS での API テンプレートの選択](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="2a97c-167">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 3.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework** .</span></span> <span data-ttu-id="2a97c-168">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-168">Select **Next** .</span></span>

* <span data-ttu-id="2a97c-169">**[プロジェクト名]** に「 *TodoApi* 」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-169">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="2a97c-171">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-171">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="2a97c-172">プロジェクトをテストする</span><span class="sxs-lookup"><span data-stu-id="2a97c-172">Test the project</span></span>

<span data-ttu-id="2a97c-173">プロジェクト テンプレートにより、[Swagger](xref:tutorials/web-api-help-pages-using-swagger) をサポートする `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a97c-175">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2a97c-176">Visual Studio により、以下が起動されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-176">Visual Studio launches:</span></span>

* <span data-ttu-id="2a97c-177">IIS Express Web サーバー。</span><span class="sxs-lookup"><span data-stu-id="2a97c-177">The IIS Express web server.</span></span>
* <span data-ttu-id="2a97c-178">既定のブラウザーです。`https://localhost:<port>/https://localhost:5001/swagger/index.html` に移動します。`<port>` はランダムに選択されるポート番号です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-178">The default browser and navigates to `https://localhost:<port>/https://localhost:5001/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="2a97c-180">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a97c-181">ブラウザーで、次の URL に移動します: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="2a97c-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-182">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a97c-183">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="2a97c-184">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="2a97c-185">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="2a97c-186">URL に `/swagger` を追加します (URL を `https://localhost:<port>/swagger` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="2a97c-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="2a97c-187">Swagger ページ `/swagger/index.html` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="2a97c-188">**[取得]**  >  **[試してみる]**  >  **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-188">Select **GET** > **Try it out** > **Execute** .</span></span> <span data-ttu-id="2a97c-189">ページに以下が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-189">The page displays:</span></span>

* <span data-ttu-id="2a97c-190">WeatherForecast API をテストするための [Curl](https://curl.haxx.se/) コマンド。</span><span class="sxs-lookup"><span data-stu-id="2a97c-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="2a97c-191">WeatherForecast API をテストする URL。</span><span class="sxs-lookup"><span data-stu-id="2a97c-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="2a97c-192">応答コード、本文、およびヘッダー。</span><span class="sxs-lookup"><span data-stu-id="2a97c-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="2a97c-193">メディアの種類と、値とスキーマの例を含むドロップ ダウン リスト ボックス。</span><span class="sxs-lookup"><span data-stu-id="2a97c-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="2a97c-194">Swagger は、Web API の有用なドキュメントやヘルプ ページを生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="2a97c-195">このチュートリアルでは、Web API の作成について説明します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="2a97c-196">Swagger の詳細については、<xref:tutorials/web-api-help-pages-using-swagger> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="2a97c-197">ブラウザーで **要求 URL** をコピーして貼り付けます: `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="2a97c-197">Copy and past the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="2a97c-198">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-198">JSON similar to the following is returned:</span></span>

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

### <a name="update-the-launchurl"></a><span data-ttu-id="2a97c-199">launchUrl を更新する</span><span class="sxs-lookup"><span data-stu-id="2a97c-199">Update the launchUrl</span></span>

<span data-ttu-id="2a97c-200">*Properties\launchSettings.json* で、`launchUrl` を `"swagger"` から `"api/TodoItems"` に更新します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-200">In *Properties\launchSettings.json* , update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="2a97c-201">Swagger が削除されているため、上記のマークアップにより、開始される URL が、次のセクションで追加されるコントローラーの GET メソッドに変更されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="2a97c-202">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-202">Add a model class</span></span>

<span data-ttu-id="2a97c-203">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="2a97c-204">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-206">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-206">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="2a97c-207">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-207">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="2a97c-208">フォルダーに「 *:::no-loc(Models):::* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-208">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="2a97c-209">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-209">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="2a97c-210">クラスに「 *TodoItem* 」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-210">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="2a97c-211">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a97c-213">*:::no-loc(Models):::* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-213">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="2a97c-214">次のコードを使用して、`TodoItem` クラスを *:::no-loc(Models):::* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-214">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a97c-216">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-216">Right-click the project.</span></span> <span data-ttu-id="2a97c-217">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-217">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="2a97c-218">フォルダーに「 *:::no-loc(Models):::* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-218">Name the folder *:::no-loc(Models):::* .</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="2a97c-220">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-220">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="2a97c-221">クラスに「 *TodoItem* 」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-221">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="2a97c-222">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="2a97c-223">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="2a97c-224">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *:::no-loc(Models):::* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-224">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="2a97c-225">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-225">Add a database context</span></span>

<span data-ttu-id="2a97c-226">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="2a97c-227">このクラスは <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="2a97c-229">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="2a97c-229">Add NuGet packages</span></span>

* <span data-ttu-id="2a97c-230">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution** .</span></span>
* <span data-ttu-id="2a97c-231">**[参照]** タブを選択し、「Microsoft.</span><span class="sxs-lookup"><span data-stu-id="2a97c-231">Select the **Browse** tab, and then enter \*\*Microsoft.</span></span>
<span data-ttu-id="2a97c-232">**EntityFrameworkCore.SqlServer** 」と検索ボックスに入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-232">**EntityFrameworkCore.SqlServer** in the search box.</span></span>
<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Delete this line at RTM -->
* <span data-ttu-id="2a97c-233">5\.0 RC バージョンを使用できるように、 **[プレリリースを含める]** チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-233">Select the **Include prerelease** checkbox so the 5.0 RC version is available.</span></span> 
* <span data-ttu-id="2a97c-234">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-234">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="2a97c-235">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-235">Select the **Project** check box in the right pane and then select **Install** .</span></span>
* <span data-ttu-id="2a97c-236">前の手順を使用して、 **Microsoft.EntityFrameworkCore.InMemory** NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-236">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

<!-- https://github.com/dotnet/AspNetCore.Docs/issues/19782 Update this image at RTM -->
<span data-ttu-id="2a97c-237">![NuGet パッケージ マネージャー](first-web-api/_static/5/vsNuGet.png)</span><span class="sxs-lookup"><span data-stu-id="2a97c-237">![NuGet Package Manager](first-web-api/_static/5/vsNuGet.png)</span></span>

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="2a97c-238">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-238">Add the TodoContext database context</span></span>

* <span data-ttu-id="2a97c-239">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-239">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="2a97c-240">クラスに「 *TodoContext* 」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-240">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-241">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a97c-242">*:::no-loc(Models):::* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-242">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="2a97c-243">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-243">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="2a97c-244">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="2a97c-244">Register the database context</span></span>

<span data-ttu-id="2a97c-245">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-245">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2a97c-246">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-246">The container provides the service to controllers.</span></span>

<span data-ttu-id="2a97c-247">次のコードを使用して *Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-247">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="2a97c-248">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-248">The preceding code:</span></span>

* <span data-ttu-id="2a97c-249">Swagger 呼び出しを削除します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-249">Removes the Swagger calls.</span></span>
* <span data-ttu-id="2a97c-250">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-250">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="2a97c-251">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-251">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="2a97c-252">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-252">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="2a97c-253">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2a97c-253">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-254">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-255">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-255">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="2a97c-256">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-256">Select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="2a97c-257">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-257">Select **API Controller with actions, using Entity Framework** , and then select **Add** .</span></span>
* <span data-ttu-id="2a97c-258">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-258">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="2a97c-259">**モデル クラス** で **TodoItem (TodoApi.:::no-loc(Models):::)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-259">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class** .</span></span>
  * <span data-ttu-id="2a97c-260">**データ コンテキスト クラス** で **TodoContext (TodoApi.:::no-loc(Models):::)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-260">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class** .</span></span>
  * <span data-ttu-id="2a97c-261">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-261">Select **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-262">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-262">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2a97c-263">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-263">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="2a97c-264">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-264">The preceding commands:</span></span>

* <span data-ttu-id="2a97c-265">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-265">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="2a97c-266">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-266">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="2a97c-267">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-267">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="2a97c-268">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-268">The generated code:</span></span>

* <span data-ttu-id="2a97c-269">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-269">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="2a97c-270">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-270">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="2a97c-271">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-271">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="2a97c-272">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-272">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="2a97c-273">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-273">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="2a97c-274">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-274">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="2a97c-275">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-275">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="2a97c-276">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-276">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="2a97c-277">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-277">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="2a97c-278">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-278">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="2a97c-279">PostTodoItem 作成メソッドの更新</span><span class="sxs-lookup"><span data-stu-id="2a97c-279">Update the PostTodoItem create method</span></span>

<span data-ttu-id="2a97c-280">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-280">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="2a97c-281">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-281">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="2a97c-282">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-282">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="2a97c-283">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-283">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2a97c-284"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="2a97c-284">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="2a97c-285">成功すると、[HTTP 201 状態コード](https://developer.mozilla.org/docs/Web/HTTP/Status/201)が返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-285">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="2a97c-286">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-286">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="2a97c-287">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-287">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="2a97c-288">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-288">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="2a97c-289">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-289">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="2a97c-290">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-290">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="2a97c-291">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-291">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="2a97c-292">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="2a97c-292">Install Postman</span></span>

<span data-ttu-id="2a97c-293">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-293">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="2a97c-294">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-294">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="2a97c-295">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-295">Start the web app.</span></span>
* <span data-ttu-id="2a97c-296">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-296">Start Postman.</span></span>
* <span data-ttu-id="2a97c-297">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-297">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="2a97c-298">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-298">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>
    > [!WARNING]
    > <span data-ttu-id="2a97c-299">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-299">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="2a97c-300">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-300">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="2a97c-301">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-301">Create a new request.</span></span>
* <span data-ttu-id="2a97c-302">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-302">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="2a97c-303">URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-303">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2a97c-304">たとえば、「 `https://localhost:5001/api/TodoItems` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-304">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2a97c-305">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-305">Select the **Body** tab.</span></span>
* <span data-ttu-id="2a97c-306">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-306">Select the **raw** radio button.</span></span>
* <span data-ttu-id="2a97c-307">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-307">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="2a97c-308">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-308">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="2a97c-309">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-309">Select **Send** .</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="2a97c-311">場所ヘッダー URI のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-311">Test the location header URI</span></span>

<span data-ttu-id="2a97c-312">場所ヘッダー URI は、ブラウザーでテストできます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-312">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="2a97c-313">場所ヘッダー URI をコピーしてブラウザーに貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-313">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="2a97c-314">Postman でテストするには:</span><span class="sxs-lookup"><span data-stu-id="2a97c-314">To test in Postman:</span></span>

* <span data-ttu-id="2a97c-315">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-315">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="2a97c-316">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-316">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="2a97c-318">HTTP メソッドを `GET` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-318">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="2a97c-319">URI を `https://localhost:<port>/api/TodoItems/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-319">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="2a97c-320">たとえば、「 `https://localhost:5001/api/TodoItems/1` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-320">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="2a97c-321">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-321">Select **Send** .</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="2a97c-322">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="2a97c-322">Examine the GET methods</span></span>

<span data-ttu-id="2a97c-323">2 つの GET エンドポイントが実装されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-323">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="2a97c-324">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-324">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="2a97c-325">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-325">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="2a97c-326">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-326">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="2a97c-327">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="2a97c-327">Test Get with Postman</span></span>

* <span data-ttu-id="2a97c-328">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-328">Create a new request.</span></span>
* <span data-ttu-id="2a97c-329">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-329">Set the HTTP method to **GET** .</span></span>
* <span data-ttu-id="2a97c-330">要求 URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-330">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2a97c-331">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-331">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2a97c-332">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-332">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="2a97c-333">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-333">Select **Send** .</span></span>

<span data-ttu-id="2a97c-334">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-334">This app uses an in-memory database.</span></span> <span data-ttu-id="2a97c-335">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-335">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="2a97c-336">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-336">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="2a97c-337">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="2a97c-337">Routing and URL paths</span></span>

<span data-ttu-id="2a97c-338">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-338">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="2a97c-339">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-339">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="2a97c-340">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-340">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="2a97c-341">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-341">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="2a97c-342">このサンプルでは、コントローラー クラス名は **TodoItems** Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-342">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="2a97c-343">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-343">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="2a97c-344">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-344">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="2a97c-345">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-345">This sample doesn't use a template.</span></span> <span data-ttu-id="2a97c-346">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-346">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2a97c-347">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-347">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="2a97c-348">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-348">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="2a97c-349">戻り値</span><span class="sxs-lookup"><span data-stu-id="2a97c-349">Return values</span></span>

<span data-ttu-id="2a97c-350">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-350">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="2a97c-351">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-351">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="2a97c-352">ハンドルされない例外がないと仮定すると、この戻り値の型の応答コードは [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200) です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-352">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="2a97c-353">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-353">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="2a97c-354">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-354">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="2a97c-355">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-355">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="2a97c-356">要求された ID に一致するアイテムがない場合、このメソッドにより [404 ステータス](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-356">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="2a97c-357">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-357">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="2a97c-358">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-358">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="2a97c-359">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="2a97c-359">The PutTodoItem method</span></span>

<span data-ttu-id="2a97c-360">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-360">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="2a97c-361">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-361">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="2a97c-362">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-362">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="2a97c-363">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-363">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="2a97c-364">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-364">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="2a97c-365">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-365">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="2a97c-366">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-366">Test the PutTodoItem method</span></span>

<span data-ttu-id="2a97c-367">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-367">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="2a97c-368">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-368">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="2a97c-369">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-369">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="2a97c-370">Id = 1 の To Do アイテムを更新し、その名前を `"feed fish"` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-370">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="2a97c-371">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-371">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="2a97c-373">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="2a97c-373">The DeleteTodoItem method</span></span>

<span data-ttu-id="2a97c-374">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-374">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="2a97c-375">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-375">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="2a97c-376">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-376">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="2a97c-377">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-377">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="2a97c-378">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-378">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="2a97c-379">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-379">Select **Send** .</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="2a97c-380">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="2a97c-380">Prevent over-posting</span></span>

<span data-ttu-id="2a97c-381">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-381">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="2a97c-382">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-382">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="2a97c-383">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-383">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="2a97c-384">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-384">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="2a97c-385">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-385">**DTO** is used in this article.</span></span>

<span data-ttu-id="2a97c-386">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-386">A DTO may be used to:</span></span>

* <span data-ttu-id="2a97c-387">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-387">Prevent over-posting.</span></span>
* <span data-ttu-id="2a97c-388">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="2a97c-388">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="2a97c-389">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-389">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="2a97c-390">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-390">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="2a97c-391">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-391">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="2a97c-392">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-392">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="2a97c-393">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-393">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="2a97c-394">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-394">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="2a97c-395">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-395">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="2a97c-396">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-396">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="2a97c-397">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-397">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="2a97c-398">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="2a97c-398">Call the web API with JavaScript</span></span>

<span data-ttu-id="2a97c-399">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-399">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2a97c-400">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-400">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a97c-401">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-401">Create a web API project.</span></span>
> * <span data-ttu-id="2a97c-402">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-402">Add a model class and a database context.</span></span>
> * <span data-ttu-id="2a97c-403">CRUD メソッドを使用してコントローラーのスキャフォールディング。</span><span class="sxs-lookup"><span data-stu-id="2a97c-403">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="2a97c-404">ルーティング、URL パス、戻り値を構成する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-404">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="2a97c-405">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="2a97c-405">Call the web API with Postman.</span></span>

<span data-ttu-id="2a97c-406">最後に、データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-406">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="2a97c-407">概要</span><span class="sxs-lookup"><span data-stu-id="2a97c-407">Overview</span></span>

<span data-ttu-id="2a97c-408">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-408">This tutorial creates the following API:</span></span>

|<span data-ttu-id="2a97c-409">API</span><span class="sxs-lookup"><span data-stu-id="2a97c-409">API</span></span> | <span data-ttu-id="2a97c-410">説明</span><span class="sxs-lookup"><span data-stu-id="2a97c-410">Description</span></span> | <span data-ttu-id="2a97c-411">要求本文</span><span class="sxs-lookup"><span data-stu-id="2a97c-411">Request body</span></span> | <span data-ttu-id="2a97c-412">応答本文</span><span class="sxs-lookup"><span data-stu-id="2a97c-412">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="2a97c-413">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-413">Get all to-do items</span></span> | <span data-ttu-id="2a97c-414">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-414">None</span></span> | <span data-ttu-id="2a97c-415">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="2a97c-415">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="2a97c-416">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-416">Get an item by ID</span></span> | <span data-ttu-id="2a97c-417">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-417">None</span></span> | <span data-ttu-id="2a97c-418">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-418">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="2a97c-419">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-419">Add a new item</span></span> | <span data-ttu-id="2a97c-420">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-420">To-do item</span></span> | <span data-ttu-id="2a97c-421">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-421">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="2a97c-422">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-422">Update an existing item &nbsp;</span></span> | <span data-ttu-id="2a97c-423">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-423">To-do item</span></span> | <span data-ttu-id="2a97c-424">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-424">None</span></span> |
|<span data-ttu-id="2a97c-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-425">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a97c-426">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-426">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a97c-427">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-427">None</span></span> | <span data-ttu-id="2a97c-428">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-428">None</span></span>|

<span data-ttu-id="2a97c-429">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-429">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="2a97c-435">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2a97c-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-438">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="2a97c-439">Web プロジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="2a97c-439">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-440">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-441">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-441">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="2a97c-442">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-442">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="2a97c-443">プロジェクトに「 *TodoApi* 」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-443">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="2a97c-444">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 3.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-444">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="2a97c-445">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-445">Select the **API** template and click **Create** .</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a97c-448">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-448">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2a97c-449">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-449">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="2a97c-450">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-450">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="2a97c-451">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-451">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

  <span data-ttu-id="2a97c-452">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-452">The preceding commands:</span></span>

  * <span data-ttu-id="2a97c-453">新しい Web API プロジェクトを作成し、Visual Studio Code で開きます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-453">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="2a97c-454">次のセクションで必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-454">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-455">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a97c-456">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-456">Select **File** > **New Solution** .</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="2a97c-458">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-458">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="2a97c-459">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-459">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>

  ![macOS での API テンプレートの選択](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="2a97c-461">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 3.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-461">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework** .</span></span> <span data-ttu-id="2a97c-462">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-462">Select **Next** .</span></span>

* <span data-ttu-id="2a97c-463">**[プロジェクト名]** に「 *TodoApi* 」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-463">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="2a97c-465">プロジェクト フォルダーでコマンド ターミナルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-465">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="2a97c-466">API のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-466">Test the API</span></span>

<span data-ttu-id="2a97c-467">プロジェクト テンプレートによって `WeatherForecast` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-467">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="2a97c-468">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-468">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-469">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-469">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a97c-470">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-470">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a97c-471">Visual Studio でブラウザーが起動し、`https://localhost:<port>/WeatherForecast` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-471">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="2a97c-472">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-472">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes** .</span></span> <span data-ttu-id="2a97c-473">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-473">In the **Security Warning** dialog that appears next, select **Yes** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-474">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-474">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a97c-475">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-475">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a97c-476">ブラウザーで、次の URL に移動します: `https://localhost:5001/WeatherForecast`。</span><span class="sxs-lookup"><span data-stu-id="2a97c-476">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-477">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-477">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a97c-478">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-478">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="2a97c-479">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-479">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="2a97c-480">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-480">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="2a97c-481">URL に `/WeatherForecast` を追加します (URL を `https://localhost:<port>/WeatherForecast` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="2a97c-481">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="2a97c-482">次のような JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-482">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="2a97c-483">モデル クラスの追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-483">Add a model class</span></span>

<span data-ttu-id="2a97c-484">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-484">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="2a97c-485">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-485">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-487">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-487">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="2a97c-488">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-488">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="2a97c-489">フォルダーに「 *:::no-loc(Models):::* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-489">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="2a97c-490">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-490">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="2a97c-491">クラスに「 *TodoItem* 」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-491">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="2a97c-492">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-492">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a97c-494">*:::no-loc(Models):::* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-494">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="2a97c-495">次のコードを使用して、`TodoItem` クラスを *:::no-loc(Models):::* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-495">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-496">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-496">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a97c-497">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-497">Right-click the project.</span></span> <span data-ttu-id="2a97c-498">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-498">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="2a97c-499">フォルダーに「 *:::no-loc(Models):::* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-499">Name the folder *:::no-loc(Models):::* .</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="2a97c-501">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-501">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="2a97c-502">クラスに「 *TodoItem* 」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-502">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="2a97c-503">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-503">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoItem.cs?name=snippet)]

<span data-ttu-id="2a97c-504">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-504">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="2a97c-505">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *:::no-loc(Models):::* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-505">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="2a97c-506">データベース コンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-506">Add a database context</span></span>

<span data-ttu-id="2a97c-507">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-507">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="2a97c-508">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-508">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-509">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-509">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="2a97c-510">NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="2a97c-510">Add NuGet packages</span></span>

* <span data-ttu-id="2a97c-511">**[ツール]** メニューで **[NuGet パッケージ マネージャー]、[ソリューションの NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-511">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution** .</span></span>
* <span data-ttu-id="2a97c-512">**[参照]** タブを選択し、検索ボックスに「 **Microsoft.EntityFrameworkCore.SqlServer** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-512">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="2a97c-513">左側のウィンドウで、 **[Microsoft.EntityFrameworkCore.SqlServer]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-513">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="2a97c-514">右側のウィンドウで **[プロジェクト]** チェックボックスをオンにして、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-514">Select the **Project** check box in the right pane and then select **Install** .</span></span>
* <span data-ttu-id="2a97c-515">前の手順を使用して、 **Microsoft.EntityFrameworkCore.InMemory** NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-515">Use the preceding instructions to add the **Microsoft.EntityFrameworkCore.InMemory** NuGet package.</span></span>

![NuGet パッケージ マネージャー](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="2a97c-517">TodoContext データベースコンテキストの追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-517">Add the TodoContext database context</span></span>

* <span data-ttu-id="2a97c-518">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-518">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="2a97c-519">クラスに「 *TodoContext* 」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-519">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-520">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-520">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a97c-521">*:::no-loc(Models):::* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-521">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="2a97c-522">次のコードを入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-522">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="2a97c-523">データベース コンテキストの登録</span><span class="sxs-lookup"><span data-stu-id="2a97c-523">Register the database context</span></span>

<span data-ttu-id="2a97c-524">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-524">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2a97c-525">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-525">The container provides the service to controllers.</span></span>

<span data-ttu-id="2a97c-526">次の強調表示されているコードを使用して、 *Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-526">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="2a97c-527">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-527">The preceding code:</span></span>

* <span data-ttu-id="2a97c-528">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-528">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="2a97c-529">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-529">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="2a97c-530">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-530">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="2a97c-531">コントローラーのスキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="2a97c-531">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-532">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-532">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-533">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-533">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="2a97c-534">**[追加]** > **[スキャフォールディングされた新しい項目]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-534">Select **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="2a97c-535">**[Entity Framework を使用したアクションがある API コントローラー]** を選択してから、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-535">Select **API Controller with actions, using Entity Framework** , and then select **Add** .</span></span>
* <span data-ttu-id="2a97c-536">**[Entity Framework を使用したアクションがある API コントローラー]** ダイアログで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-536">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="2a97c-537">**モデル クラス** で **TodoItem (TodoApi.:::no-loc(Models):::)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-537">Select **TodoItem (TodoApi.:::no-loc(Models):::)** in the **Model class** .</span></span>
  * <span data-ttu-id="2a97c-538">**データ コンテキスト クラス** で **TodoContext (TodoApi.:::no-loc(Models):::)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-538">Select **TodoContext (TodoApi.:::no-loc(Models):::)** in the **Data context class** .</span></span>
  * <span data-ttu-id="2a97c-539">**[追加]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-539">Select **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-540">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-540">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="2a97c-541">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-541">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="2a97c-542">上のコマンドでは以下の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-542">The preceding commands:</span></span>

* <span data-ttu-id="2a97c-543">スキャフォールディングに必要な NuGet パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-543">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="2a97c-544">スキャフォールディング エンジン (`dotnet-aspnet-codegenerator`) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-544">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="2a97c-545">`TodoItemsController` をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-545">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="2a97c-546">生成されたコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-546">The generated code:</span></span>

* <span data-ttu-id="2a97c-547">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-547">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="2a97c-548">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-548">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="2a97c-549">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-549">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="2a97c-550">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-550">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="2a97c-551">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-551">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="2a97c-552">ASP.NET Core テンプレートの対象は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-552">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="2a97c-553">ビューを含むコントローラーには、ルート テンプレートの `[action]` が含まれます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-553">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="2a97c-554">API コントローラーには、ルート テンプレートの `[action]` が含まれません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-554">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="2a97c-555">`[action]` トークンがルート テンプレート内にない場合、[アクション](xref:mvc/controllers/routing#action)名はルートから除外されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-555">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="2a97c-556">つまり、アクションの関連付けられたメソッド名は一致するルートでは使用されません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-556">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="2a97c-557">PostTodoItem 作成メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="2a97c-557">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="2a97c-558">[nameof](/dotnet/csharp/language-reference/operators/nameof) 演算子を使用するために、`PostTodoItem` で return ステートメントを置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-558">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="2a97c-559">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-559">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="2a97c-560">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-560">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="2a97c-561">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-561">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2a97c-562"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> メソッド:</span><span class="sxs-lookup"><span data-stu-id="2a97c-562">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="2a97c-563">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-563">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="2a97c-564">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-564">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="2a97c-565">応答に [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) ヘッダーが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-565">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="2a97c-566">`Location` ヘッダーでは、新しく作成された To Do アイテムの [URI](https://developer.mozilla.org/docs/Glossary/URI) が指定されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-566">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="2a97c-567">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-567">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="2a97c-568">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-568">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="2a97c-569">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-569">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="2a97c-570">Postman のインストール</span><span class="sxs-lookup"><span data-stu-id="2a97c-570">Install Postman</span></span>

<span data-ttu-id="2a97c-571">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-571">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="2a97c-572">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-572">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="2a97c-573">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-573">Start the web app.</span></span>
* <span data-ttu-id="2a97c-574">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-574">Start Postman.</span></span>
* <span data-ttu-id="2a97c-575">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-575">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="2a97c-576">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-576">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>
    > [!WARNING]
    > <span data-ttu-id="2a97c-577">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-577">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="2a97c-578">Postman を使用した PostTodoItem のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-578">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="2a97c-579">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-579">Create a new request.</span></span>
* <span data-ttu-id="2a97c-580">HTTP メソッドを `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-580">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="2a97c-581">URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-581">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2a97c-582">たとえば、「 `https://localhost:5001/api/TodoItems` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-582">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2a97c-583">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-583">Select the **Body** tab.</span></span>
* <span data-ttu-id="2a97c-584">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-584">Select the **raw** radio button.</span></span>
* <span data-ttu-id="2a97c-585">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-585">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="2a97c-586">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-586">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="2a97c-587">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-587">Select **Send** .</span></span>

  ![Postman での Create 要求](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="2a97c-589">Postman で Location ヘッダーの URI をテストする</span><span class="sxs-lookup"><span data-stu-id="2a97c-589">Test the location header URI with Postman</span></span>

* <span data-ttu-id="2a97c-590">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-590">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="2a97c-591">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-591">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/3/create.png)

* <span data-ttu-id="2a97c-593">HTTP メソッドを `GET` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-593">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="2a97c-594">URI を `https://localhost:<port>/api/TodoItems/1` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-594">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="2a97c-595">たとえば、「 `https://localhost:5001/api/TodoItems/1` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-595">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="2a97c-596">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-596">Select **Send** .</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="2a97c-597">GET メソッドの確認</span><span class="sxs-lookup"><span data-stu-id="2a97c-597">Examine the GET methods</span></span>

<span data-ttu-id="2a97c-598">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-598">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="2a97c-599">ブラウザーまたは Postman から 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-599">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="2a97c-600">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-600">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="2a97c-601">`GetTodoItems` への呼び出しによって、次のような応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-601">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="2a97c-602">Postman を使用して Get をテストする</span><span class="sxs-lookup"><span data-stu-id="2a97c-602">Test Get with Postman</span></span>

* <span data-ttu-id="2a97c-603">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-603">Create a new request.</span></span>
* <span data-ttu-id="2a97c-604">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-604">Set the HTTP method to **GET** .</span></span>
* <span data-ttu-id="2a97c-605">要求 URI を `https://localhost:<port>/api/TodoItems` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-605">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="2a97c-606">たとえば、`https://localhost:5001/api/TodoItems` のようにします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-606">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="2a97c-607">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-607">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="2a97c-608">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-608">Select **Send** .</span></span>

<span data-ttu-id="2a97c-609">このアプリではメモリ内データベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-609">This app uses an in-memory database.</span></span> <span data-ttu-id="2a97c-610">アプリが停止して開始された場合、上記の GET 要求はデータを返しません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-610">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="2a97c-611">データが返されない場合は、アプリにデータを [POST](#post) します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-611">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="2a97c-612">ルーティングと URL パス</span><span class="sxs-lookup"><span data-stu-id="2a97c-612">Routing and URL paths</span></span>

<span data-ttu-id="2a97c-613">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-613">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="2a97c-614">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-614">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="2a97c-615">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-615">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="2a97c-616">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-616">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="2a97c-617">このサンプルでは、コントローラー クラス名は **TodoItems** Controller なので、コントローラー名は "TodoItems" です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-617">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="2a97c-618">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-618">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="2a97c-619">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-619">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="2a97c-620">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-620">This sample doesn't use a template.</span></span> <span data-ttu-id="2a97c-621">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-621">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2a97c-622">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-622">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="2a97c-623">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-623">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="2a97c-624">戻り値</span><span class="sxs-lookup"><span data-stu-id="2a97c-624">Return values</span></span> 

<span data-ttu-id="2a97c-625">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-625">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="2a97c-626">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-626">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="2a97c-627">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-627">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="2a97c-628">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-628">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="2a97c-629">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-629">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="2a97c-630">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-630">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="2a97c-631">要求された ID と一致するアイテムがない場合、メソッドは 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-631">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="2a97c-632">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-632">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="2a97c-633">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-633">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="2a97c-634">PutTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="2a97c-634">The PutTodoItem method</span></span>

<span data-ttu-id="2a97c-635">`PutTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-635">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="2a97c-636">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-636">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="2a97c-637">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-637">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="2a97c-638">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-638">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="2a97c-639">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-639">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="2a97c-640">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-640">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="2a97c-641">PutTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-641">Test the PutTodoItem method</span></span>

<span data-ttu-id="2a97c-642">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-642">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="2a97c-643">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-643">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="2a97c-644">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-644">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="2a97c-645">Id = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-645">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="2a97c-646">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-646">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="2a97c-648">DeleteTodoItem メソッド</span><span class="sxs-lookup"><span data-stu-id="2a97c-648">The DeleteTodoItem method</span></span>

<span data-ttu-id="2a97c-649">`DeleteTodoItem` メソッドを検証します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-649">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="2a97c-650">DeleteTodoItem メソッドのテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-650">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="2a97c-651">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-651">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="2a97c-652">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-652">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="2a97c-653">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/TodoItems/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-653">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="2a97c-654">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-654">Select **Send** .</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="2a97c-655">過剰な投稿を防止する</span><span class="sxs-lookup"><span data-stu-id="2a97c-655">Prevent over-posting</span></span>

<span data-ttu-id="2a97c-656">現在、サンプル アプリでは `TodoItem` オブジェクト全体が公開されています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-656">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="2a97c-657">通常、運用環境のアプリでは、モデルのサブセットを使用して入力されるデータおよび返されるデータが制限されています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-657">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="2a97c-658">その背景には複数の理由があり、セキュリティは主なものです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-658">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="2a97c-659">モデルのサブセットは、通常、データ転送オブジェクト (DTO)、入力モデル、またはビュー モデルと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-659">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="2a97c-660">この記事では **DTO** を使用しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-660">**DTO** is used in this article.</span></span>

<span data-ttu-id="2a97c-661">DTO は次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-661">A DTO may be used to:</span></span>

* <span data-ttu-id="2a97c-662">過剰な投稿を防止する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-662">Prevent over-posting.</span></span>
* <span data-ttu-id="2a97c-663">クライアントが表示しないことになっているプロパティを非表示にする。</span><span class="sxs-lookup"><span data-stu-id="2a97c-663">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="2a97c-664">ペイロード サイズを減らすために、いくつかのプロパティを省略する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-664">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="2a97c-665">入れ子になったオブジェクトを含むオブジェクト グラフをフラット化する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-665">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="2a97c-666">フラット化されたオブジェクト グラフは、クライアントにとってより便利になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-666">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="2a97c-667">DTO のアプローチを実演するために、`TodoItem` クラスを更新して、シークレット フィールドを含めます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-667">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="2a97c-668">シークレット フィールドは、このアプリでは非表示にする必要がありますが、管理アプリの場合は公開することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-668">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="2a97c-669">シークレット フィールドを投稿および取得できることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-669">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="2a97c-670">次のように DTO モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-670">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/:::no-loc(Models):::/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="2a97c-671">`TodoItemDTO` を使用するように `TodoItemsController` を更新します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-671">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="2a97c-672">シークレット フィールドを投稿または取得できないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-672">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="2a97c-673">JavaScript を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="2a97c-673">Call the web API with JavaScript</span></span>

<span data-ttu-id="2a97c-674">「[チュートリアル:JavaScript を使用して ASP.NET Core Web API を呼び出す](xref:tutorials/web-api-javascript)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-674">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a97c-675">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-675">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2a97c-676">Web API プロジェクトを作成する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-676">Create a web API project.</span></span>
> * <span data-ttu-id="2a97c-677">モデル クラスとデータベース コンテキストを追加する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-677">Add a model class and a database context.</span></span>
> * <span data-ttu-id="2a97c-678">コントローラーを追加する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-678">Add a controller.</span></span>
> * <span data-ttu-id="2a97c-679">CRUD メソッドを追加する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-679">Add CRUD methods.</span></span>
> * <span data-ttu-id="2a97c-680">ルーティングと URL パスを構成する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-680">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="2a97c-681">戻り値を指定する。</span><span class="sxs-lookup"><span data-stu-id="2a97c-681">Specify return values.</span></span>
> * <span data-ttu-id="2a97c-682">Postman で Web API を呼び出す。</span><span class="sxs-lookup"><span data-stu-id="2a97c-682">Call the web API with Postman.</span></span>
> * <span data-ttu-id="2a97c-683">JavaScript を使用した Web API の呼び出し。</span><span class="sxs-lookup"><span data-stu-id="2a97c-683">Call the web API with JavaScript.</span></span>

<span data-ttu-id="2a97c-684">最後に、リレーショナル データベースに格納されている "To Do" アイテムを管理できる Web API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-684">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="2a97c-685">概要 2.1</span><span class="sxs-lookup"><span data-stu-id="2a97c-685">Overview 2.1</span></span>

<span data-ttu-id="2a97c-686">このチュートリアルでは、次の API を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-686">This tutorial creates the following API:</span></span>

|<span data-ttu-id="2a97c-687">API</span><span class="sxs-lookup"><span data-stu-id="2a97c-687">API</span></span> | <span data-ttu-id="2a97c-688">説明</span><span class="sxs-lookup"><span data-stu-id="2a97c-688">Description</span></span> | <span data-ttu-id="2a97c-689">要求本文</span><span class="sxs-lookup"><span data-stu-id="2a97c-689">Request body</span></span> | <span data-ttu-id="2a97c-690">応答本文</span><span class="sxs-lookup"><span data-stu-id="2a97c-690">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="2a97c-691">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="2a97c-691">GET /api/TodoItems</span></span> | <span data-ttu-id="2a97c-692">すべての To Do アイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-692">Get all to-do items</span></span> | <span data-ttu-id="2a97c-693">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-693">None</span></span> | <span data-ttu-id="2a97c-694">To Do アイテムの配列</span><span class="sxs-lookup"><span data-stu-id="2a97c-694">Array of to-do items</span></span>|
|<span data-ttu-id="2a97c-695">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="2a97c-695">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="2a97c-696">ID でアイテムを取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-696">Get an item by ID</span></span> | <span data-ttu-id="2a97c-697">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-697">None</span></span> | <span data-ttu-id="2a97c-698">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-698">To-do item</span></span>|
|<span data-ttu-id="2a97c-699">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="2a97c-699">POST /api/TodoItems</span></span> | <span data-ttu-id="2a97c-700">新しいアイテムを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-700">Add a new item</span></span> | <span data-ttu-id="2a97c-701">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-701">To-do item</span></span> | <span data-ttu-id="2a97c-702">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-702">To-do item</span></span> |
|<span data-ttu-id="2a97c-703">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="2a97c-703">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="2a97c-704">既存のアイテムを更新します。&nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-704">Update an existing item &nbsp;</span></span> | <span data-ttu-id="2a97c-705">To Do アイテム</span><span class="sxs-lookup"><span data-stu-id="2a97c-705">To-do item</span></span> | <span data-ttu-id="2a97c-706">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-706">None</span></span> |
|<span data-ttu-id="2a97c-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-707">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a97c-708">アイテムを削除します &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="2a97c-708">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="2a97c-709">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-709">None</span></span> | <span data-ttu-id="2a97c-710">None</span><span class="sxs-lookup"><span data-stu-id="2a97c-710">None</span></span>|

<span data-ttu-id="2a97c-711">次の図は、アプリのデザインを示しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-711">The following diagram shows the design of the app.</span></span>

![クライアントは、左側のボックスに表されます。](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="2a97c-717">前提条件 2.1</span><span class="sxs-lookup"><span data-stu-id="2a97c-717">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-718">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-719">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-719">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-720">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-720">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="2a97c-721">Web プロジェクト 2.1 の作成</span><span class="sxs-lookup"><span data-stu-id="2a97c-721">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-722">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-722">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-723">**[ファイル]** メニューで、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-723">From the **File** menu, select **New** > **Project** .</span></span>
* <span data-ttu-id="2a97c-724">**[ASP.NET Core Web アプリケーション]** テンプレートを選択して、 **[次へ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-724">Select the **ASP.NET Core Web Application** template and click **Next** .</span></span>
* <span data-ttu-id="2a97c-725">プロジェクトに「 *TodoApi* 」という名前を付け、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-725">Name the project *TodoApi* and click **Create** .</span></span>
* <span data-ttu-id="2a97c-726">**[新しい ASP.NET Core Web アプリケーションを作成する]** ダイアログで、 **[.NET Core]** と **[ASP.NET Core 2.2]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-726">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="2a97c-727">**API** テンプレートを選択し、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-727">Select the **API** template and click **Create** .</span></span> <span data-ttu-id="2a97c-728">**[Enable Docker Support]\(Docker サポートを有効にする\)** は **選択しないで** ください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-728">**Don't** select **Enable Docker Support** .</span></span>

![VS の [新しいプロジェクト] ダイアログ](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-730">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-730">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a97c-731">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-731">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2a97c-732">ディレクトリ (`cd`) を、プロジェクト フォルダーを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-732">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="2a97c-733">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-733">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="2a97c-734">これらのコマンドでは、新しい Web API プロジェクトが作成され、新しいプロジェクト フォルダー内に Visual Studio Code の新しいインスタンスが開かれます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-734">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="2a97c-735">ダイアログ ボックスで、プロジェクトに必要な資産を追加するかどうかを確認されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-735">When a dialog box asks if you want to add required assets to the project, select **Yes** .</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-736">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-736">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a97c-737">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-737">Select **File** > **New Solution** .</span></span>

  ![macOS の新しいソリューション](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="2a97c-739">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]**  >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-739">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next** .</span></span> <span data-ttu-id="2a97c-740">バージョン 8.6 以降では、 **[Web and Console]** \(Web とコンソール\) >  **[アプリ]**  >  **[API]**  >  **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-740">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next** .</span></span>
  
* <span data-ttu-id="2a97c-741">**[Configure the new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、最新の .NET Core 2.x **ターゲット フレームワーク** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-741">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework** .</span></span> <span data-ttu-id="2a97c-742">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-742">Select **Next** .</span></span>

* <span data-ttu-id="2a97c-743">**[プロジェクト名]** に「 *TodoApi* 」と入力し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-743">Enter *TodoApi* for the **Project Name** and then select **Create** .</span></span>

  ![構成ダイアログ](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="2a97c-745">API 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-745">Test the API 2.1</span></span>

<span data-ttu-id="2a97c-746">プロジェクト テンプレートによって `values` API が作成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-746">The project template creates a `values` API.</span></span> <span data-ttu-id="2a97c-747">ブラウザーから `Get` メソッドを呼び出して、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-747">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-748">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-748">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2a97c-749">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-749">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a97c-750">Visual Studio でブラウザーが起動し、`https://localhost:<port>/api/values` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-750">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="2a97c-751">IIS Express 証明書を信頼するかどうかを確認するダイアログ ボックスが表示された場合は、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-751">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes** .</span></span> <span data-ttu-id="2a97c-752">次に表示される **[セキュリティ警告]** ダイアログ ボックスで、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-752">In the **Security Warning** dialog that appears next, select **Yes** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-753">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-753">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a97c-754">Ctrl キーを押しながら F5 キーを押して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-754">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="2a97c-755">ブラウザーで、次の URL に移動します: `https://localhost:5001/api/values`。</span><span class="sxs-lookup"><span data-stu-id="2a97c-755">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-756">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-756">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2a97c-757">**[実行]**  >  **[デバッグの開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-757">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="2a97c-758">Visual Studio for Mac でブラウザーが起動し、`https://localhost:<port>` にアクセスします。ここで、`<port>` はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-758">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="2a97c-759">HTTP 404 (Not Found) エラーが返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-759">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="2a97c-760">URL に `/api/values` を追加します (URL を `https://localhost:<port>/api/values` に変更します)。</span><span class="sxs-lookup"><span data-stu-id="2a97c-760">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="2a97c-761">次の JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-761">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="2a97c-762">モデル クラス 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-762">Add a model class 2.1</span></span>

<span data-ttu-id="2a97c-763">*モデル* は、アプリが管理するデータを表すクラスのセットです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-763">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="2a97c-764">このアプリのモデルは、単一の `TodoItem` クラスです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-764">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-765">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-765">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-766">**ソリューション エクスプローラー** で、プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-766">In **Solution Explorer** , right-click the project.</span></span> <span data-ttu-id="2a97c-767">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-767">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="2a97c-768">フォルダーに「 *:::no-loc(Models):::* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-768">Name the folder *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="2a97c-769">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-769">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="2a97c-770">クラスに「 *TodoItem* 」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-770">Name the class *TodoItem* and select **Add** .</span></span>

* <span data-ttu-id="2a97c-771">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-771">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2a97c-772">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a97c-772">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2a97c-773">*:::no-loc(Models):::* という名前のフォルダーを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-773">Add a folder named *:::no-loc(Models):::* .</span></span>

* <span data-ttu-id="2a97c-774">次のコードを使用して、`TodoItem` クラスを *:::no-loc(Models):::* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-774">Add a `TodoItem` class to the *:::no-loc(Models):::* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-775">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-775">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a97c-776">プロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-776">Right-click the project.</span></span> <span data-ttu-id="2a97c-777">**[追加]**  >  **[新しいフォルダー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-777">Select **Add** > **New Folder** .</span></span> <span data-ttu-id="2a97c-778">フォルダーに「 *:::no-loc(Models):::* 」という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-778">Name the folder *:::no-loc(Models):::* .</span></span>

  ![新しいフォルダー](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="2a97c-780">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]** > **[新しいファイル]** > **[全般]** > **[空のクラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-780">Right-click the *:::no-loc(Models):::* folder, and select **Add** > **New File** > **General** > **Empty Class** .</span></span>

* <span data-ttu-id="2a97c-781">クラスに「 *TodoItem* 」という名前を付け、 **[新規]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-781">Name the class *TodoItem* , and then click **New** .</span></span>

* <span data-ttu-id="2a97c-782">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-782">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoItem.cs)]

<span data-ttu-id="2a97c-783">`Id` プロパティは、リレーショナル データベース内の一意のキーとして機能します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-783">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="2a97c-784">モデル クラスはプロジェクト内のどこでも使用できますが、慣例により *:::no-loc(Models):::* フォルダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-784">Model classes can go anywhere in the project, but the *:::no-loc(Models):::* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="2a97c-785">データベース コンテキスト 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-785">Add a database context 2.1</span></span>

<span data-ttu-id="2a97c-786">*データベース コンテキスト* は、データ モデルに対して Entity Framework 機能を調整するメイン クラスです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-786">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="2a97c-787">このクラスは `Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-787">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-788">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-788">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-789">*:::no-loc(Models):::* フォルダーを右クリックして、 **[追加]**  >  **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-789">Right-click the *:::no-loc(Models):::* folder and select **Add** > **Class** .</span></span> <span data-ttu-id="2a97c-790">クラスに「 *TodoContext* 」という名前を付け、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-790">Name the class *TodoContext* and click **Add** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-791">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-791">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a97c-792">*:::no-loc(Models):::* フォルダーに `TodoContext` クラスを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-792">Add a `TodoContext` class to the *:::no-loc(Models):::* folder.</span></span>

---

* <span data-ttu-id="2a97c-793">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-793">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/:::no-loc(Models):::/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="2a97c-794">データベース コンテキスト 2.1 の登録</span><span class="sxs-lookup"><span data-stu-id="2a97c-794">Register the database context 2.1</span></span>

<span data-ttu-id="2a97c-795">ASP.NET Core で、サービス (DB コンテキストなど) を[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection)コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-795">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2a97c-796">コンテナーは、コントローラーにサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-796">The container provides the service to controllers.</span></span>

<span data-ttu-id="2a97c-797">次の強調表示されているコードを使用して、 *Startup.cs* を更新します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-797">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="2a97c-798">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-798">The preceding code:</span></span>

* <span data-ttu-id="2a97c-799">不要な `using` 宣言を削除します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-799">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="2a97c-800">DI コンテナーにデータベース コンテキストを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-800">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="2a97c-801">データベース コンテキストがメモリ内データベースを使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-801">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="2a97c-802">コントローラー 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-802">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-803">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-803">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-804">*Controllers* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-804">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="2a97c-805">**[追加]** > **[新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-805">Select **Add** > **New Item** .</span></span>
* <span data-ttu-id="2a97c-806">**[新しい項目の追加]** ダイアログで、 **[API コントローラー クラス]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-806">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="2a97c-807">クラスに「 *TodoController* 」という名前を付け、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-807">Name the class *TodoController* , and select **Add** .</span></span>

  ![[新しい項目の追加] ダイアログ。検索ボックスに「controller」と入力されています。Web API コントローラーが選択されています。](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-809">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-809">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a97c-810">*Controllers* フォルダーで、「`TodoController`」という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-810">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="2a97c-811">テンプレート コードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-811">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="2a97c-812">上記のコードでは次の操作が行われます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-812">The preceding code:</span></span>

* <span data-ttu-id="2a97c-813">メソッドを使用せず、API コントローラー クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-813">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="2a97c-814">クラスを [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性でマークします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-814">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="2a97c-815">この属性は、コントローラーが Web API 要求に応答することを示します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-815">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="2a97c-816">属性によって有効化される特定の動作については、「<xref:web-api/index>」 を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-816">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="2a97c-817">DI を使用して、データベース コンテキスト (`TodoContext`) をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-817">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="2a97c-818">データベース コンテキストは、コントローラーの各 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) メソッドで使用されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-818">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="2a97c-819">追加データベースが空の場合、`Item1` という名前のアイテムをデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-819">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="2a97c-820">このコードはコンストラクター内にあるので、新しい HTTP 要求が行われるたびに実行されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-820">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="2a97c-821">すべてのアイテムを削除した場合、コンストラクターは、次回に API メソッドが呼び出されたときに `Item1` をもう一度作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-821">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="2a97c-822">そのため、削除が実際には機能していても、機能しなかったように見える場合があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-822">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="2a97c-823">Get メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-823">Add Get methods 2.1</span></span>

<span data-ttu-id="2a97c-824">To Do アイテムを取得する API を指定するには、`TodoController` クラスに次のメソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-824">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="2a97c-825">これらのメソッドは、次の 2 つの GET エンドポイントを実装します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-825">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="2a97c-826">アプリがまだ実行中の場合は、停止します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-826">Stop the app if it's still running.</span></span> <span data-ttu-id="2a97c-827">次に、それを再度実行して、最新の変更を含めます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-827">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="2a97c-828">ブラウザーからこれらの 2 つのエンドポイントを呼び出すことによって、アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-828">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="2a97c-829">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-829">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="2a97c-830">`GetTodoItems` への呼び出しによって、次の HTTP 応答が生成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-830">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="2a97c-831">ルーティングと URL パス 2.1</span><span class="sxs-lookup"><span data-stu-id="2a97c-831">Routing and URL paths 2.1</span></span>

<span data-ttu-id="2a97c-832">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) 属性は、HTTP GET 要求に応答するメソッドを表します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-832">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="2a97c-833">各メソッドの URL パスは次のように構成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-833">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="2a97c-834">コントローラーの `Route` 属性でテンプレート文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-834">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="2a97c-835">`[controller]` をコントローラーの名前 (慣例では "Controller" サフィックスを除くコントローラー クラス名) に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-835">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="2a97c-836">このサンプルでは、コントローラー クラス名は **Todo** Controller なので、コントローラー名は "todo" です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-836">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="2a97c-837">ASP.NET Core の[ルーティング](xref:mvc/controllers/routing)では、大文字と小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-837">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="2a97c-838">`[HttpGet]` 属性にルート テンプレート (たとえば、`[HttpGet("products")]`) がある場合は、それをパスに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-838">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="2a97c-839">このサンプルではテンプレートを使用しません。</span><span class="sxs-lookup"><span data-stu-id="2a97c-839">This sample doesn't use a template.</span></span> <span data-ttu-id="2a97c-840">詳細については、「[Http[Verb] 属性を使用する属性ルーティング](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-840">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="2a97c-841">次の `GetTodoItem` メソッドで、`"{id}"` は To Do アイテムの一意識別子に使用するプレースホルダーの変数です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-841">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="2a97c-842">`GetTodoItem` が呼び出されると、その `id` パラメーター内のメソッドに URL の `"{id}"` の値が指定されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-842">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="2a97c-843">戻り値 2.1</span><span class="sxs-lookup"><span data-stu-id="2a97c-843">Return values 2.1</span></span>

<span data-ttu-id="2a97c-844">`GetTodoItems` と `GetTodoItem` メソッドの戻り値の型は、[ActionResult\<T> 型](xref:web-api/action-return-types#actionresultt-type)です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-844">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="2a97c-845">ASP.NET Core は自動的にオブジェクトを [JSON](https://www.json.org/) にシリアル化して、応答メッセージの本文に JSON を書き込みます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-845">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="2a97c-846">この戻り値の型の応答コードは 200 で、ハンドルされない例外がないものと想定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-846">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="2a97c-847">ハンドルされない例外は 5xx エラーに変換されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-847">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="2a97c-848">`ActionResult` 戻り値の型は、幅広い範囲の HTTP 状態コードを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-848">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="2a97c-849">たとえば、`GetTodoItem` は、次の 2 つの異なる状態値を返す可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-849">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="2a97c-850">要求された ID と一致するアイテムがない場合、メソッドは 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> エラー コードを返します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-850">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="2a97c-851">それ以外の場合、メソッドは JSON 応答本文で 200 を返します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-851">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="2a97c-852">戻り値が `item` の場合、HTTP 200 応答が返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-852">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="2a97c-853">GetTodoItems メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-853">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="2a97c-854">このチュートリアルでは、Postman を使用して Web API をテストします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-854">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="2a97c-855">[Postman](https://www.getpostman.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-855">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="2a97c-856">Web アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-856">Start the web app.</span></span>
* <span data-ttu-id="2a97c-857">Postman を起動します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-857">Start Postman.</span></span>
* <span data-ttu-id="2a97c-858">**[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-858">Disable **SSL certificate verification** .</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2a97c-859">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a97c-859">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a97c-860">**[ファイル]** > **[設定]** ( **[全般]** タブ) で、 **[SSL 証明書の確認]** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-860">From **File** > **Settings** ( **General** tab), disable **SSL certificate verification** .</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="2a97c-861">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2a97c-861">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="2a97c-862">**[Postman]**  >  **[ユーザー設定]** ( **[全般]** タブ) で、 **SSL 証明書の検証** を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-862">From **Postman** > **Preferences** ( **General** tab), disable **SSL certificate verification** .</span></span> <span data-ttu-id="2a97c-863">あるいは、レンチを選択し、 **[設定]** を選択して、SSL 証明書の検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-863">Alternatively, select the wrench and select **Settings** , then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="2a97c-864">コントローラーをテストした後、SSL 証明書の検証を再度有効にします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-864">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="2a97c-865">新しい要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-865">Create a new request.</span></span>
  * <span data-ttu-id="2a97c-866">HTTP メソッドを **GET** に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-866">Set the HTTP method to **GET** .</span></span>
  * <span data-ttu-id="2a97c-867">要求 URI を `https://localhost:<port>/api/todo` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-867">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="2a97c-868">たとえば、`https://localhost:5001/api/todo` のようにします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-868">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="2a97c-869">Postman で **[Two pane view]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-869">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="2a97c-870">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-870">Select **Send** .</span></span>

![Postman での Get 要求](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="2a97c-872">Create メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-872">Add a Create method 2.1</span></span>

<span data-ttu-id="2a97c-873">*Controllers/TodoController.cs* 内に次の `PostTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-873">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs* :</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="2a97c-874">[`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) 属性が示すように、上記のコードは HTTP POST メソッドです。</span><span class="sxs-lookup"><span data-stu-id="2a97c-874">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="2a97c-875">このメソッドは、HTTP 要求の本文から To Do アイテムの値を取得します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-875">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="2a97c-876">`CreatedAtAction` メソッド:</span><span class="sxs-lookup"><span data-stu-id="2a97c-876">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="2a97c-877">成功すると、HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-877">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="2a97c-878">HTTP 201 は、サーバーに新しいリソースを作成する HTTP POST メソッドに対する標準の応答です。</span><span class="sxs-lookup"><span data-stu-id="2a97c-878">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="2a97c-879">`Location` ヘッダーを応答に追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-879">Adds a `Location` header to the response.</span></span> <span data-ttu-id="2a97c-880">`Location` ヘッダーでは、新しく作成された To Do アイテムの URI を指定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-880">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="2a97c-881">詳細については、「[10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-881">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="2a97c-882">`GetTodoItem` アクションを参照して `Location` ヘッダーの URI を作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-882">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="2a97c-883">C# の `nameof` キーワードを使って、`CreatedAtAction` 呼び出しでアクション名をハードコーディングすることを回避しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-883">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="2a97c-884">PostTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-884">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="2a97c-885">プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-885">Build the project.</span></span>
* <span data-ttu-id="2a97c-886">Postman で、HTTP メソッド名を `POST` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-886">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="2a97c-887">URI を `https://localhost:<port>/api/TodoItem` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-887">Set the URI to `https://localhost:<port>/api/TodoItem`.</span></span> <span data-ttu-id="2a97c-888">たとえば、「 `https://localhost:5001/api/TodoItem` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-888">For example, `https://localhost:5001/api/TodoItem`.</span></span>
* <span data-ttu-id="2a97c-889">**[Body]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-889">Select the **Body** tab.</span></span>
* <span data-ttu-id="2a97c-890">**[raw]** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-890">Select the **raw** radio button.</span></span>
* <span data-ttu-id="2a97c-891">型を **[JSON (application/json)]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-891">Set the type to **JSON (application/json)** .</span></span>
* <span data-ttu-id="2a97c-892">要求本文に、To Do アイテムの JSON を入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-892">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="2a97c-893">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-893">Select **Send** .</span></span>

  ![Postman での Create 要求](first-web-api/_static/create.png)

  <span data-ttu-id="2a97c-895">405 (Method Not Allowed) エラーが発生した場合は、`PostTodoItem` メソッドの追加後にプロジェクトをコンパイルしていないことが原因である可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-895">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="2a97c-896">場所ヘッダー URI 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-896">Test the location header URI 2.1</span></span>

* <span data-ttu-id="2a97c-897">**[Response]** ウィンドウで、 **[Headers]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-897">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="2a97c-898">**[Location]** ヘッダー値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="2a97c-898">Copy the **Location** header value:</span></span>

  ![Postman コンソールの [Headers] タブ](first-web-api/_static/pmc2.png)

* <span data-ttu-id="2a97c-900">メソッドを GET に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-900">Set the method to GET.</span></span>
* <span data-ttu-id="2a97c-901">URI を `https://localhost:<port>/api/TodoItems/2` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-901">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="2a97c-902">たとえば、「 `https://localhost:5001/api/TodoItems/2` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-902">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="2a97c-903">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-903">Select **Send** .</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="2a97c-904">PutTodoItem メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-904">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="2a97c-905">次の `PutTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-905">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="2a97c-906">`PutTodoItem` は `PostTodoItem` と似ていますが、HTTP PUT を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-906">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="2a97c-907">応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-907">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="2a97c-908">HTTP 仕様に従って、PUT 要求では、変更だけでなく、更新されたエンティティ全体を送信するようクライアントに求めます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-908">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="2a97c-909">部分的な更新をサポートするには、[HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute) を使用します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-909">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="2a97c-910">`PutTodoItem` 呼び出しでエラーが発生した場合、`GET` を呼び出してデータベース内にアイテムがあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-910">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="2a97c-911">PutTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-911">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="2a97c-912">このサンプルでは、アプリを起動するたびに開始することが必要なメモリ内データベースが使われています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-912">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="2a97c-913">PUT 呼び出しを実行する前に、データベース内にアイテムが存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-913">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="2a97c-914">GET を呼び出して、PUT 呼び出しを実行する前にデータベース内にアイテムが確実に存在していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-914">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="2a97c-915">Id = 1 の To Do アイテムを更新し、その名前を "feed fish" に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-915">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="2a97c-916">次の図は、Postman の更新を示しています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-916">The following image shows the Postman update:</span></span>

![204 (No Content) の応答を示す Postman コンソール](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="2a97c-918">DeleteTodoItem メソッド 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-918">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="2a97c-919">次の `DeleteTodoItem` メソッドを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-919">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="2a97c-920">`DeleteTodoItem` の応答は [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) となります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-920">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="2a97c-921">DeleteTodoItem メソッド 2.1 のテスト</span><span class="sxs-lookup"><span data-stu-id="2a97c-921">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="2a97c-922">Postman を使用して、To Do アイテムを削除します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-922">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="2a97c-923">メソッドを `DELETE` に設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-923">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="2a97c-924">削除するオブジェクトの URI (たとえば、`https://localhost:5001/api/todo/1`) を設定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-924">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="2a97c-925">**[Send]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-925">Select **Send** .</span></span>

<span data-ttu-id="2a97c-926">サンプル アプリではすべてのアイテムを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-926">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="2a97c-927">ただし、最後のアイテムが削除されると、次回 API が呼び出されたときに、モデル クラス コンストラクターによって新しいアイテムが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-927">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="2a97c-928">JavaScript 2.1 を使用した Web API の呼び出し</span><span class="sxs-lookup"><span data-stu-id="2a97c-928">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="2a97c-929">このセクションでは、JavaScript を使用して Web API を呼び出す HTML ページを追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-929">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="2a97c-930">jQuery によって要求が開始されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-930">jQuery initiates the request.</span></span> <span data-ttu-id="2a97c-931">JavaScript により、Web API の応答からの詳細を使ってページが更新されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-931">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="2a97c-932">*Startup.cs* を次の強調表示されたコードで更新して、 [静的ファイルを提供](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A)し、 [既定のファイル マッピングを有効にする](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A)ためのアプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-932">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="2a97c-933">プロジェクト ディレクトリで *wwwroot* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-933">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="2a97c-934">*index.html* という名前の HTML ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-934">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="2a97c-935">その内容を次のマークアップに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-935">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="2a97c-936">*site.js* という名前の JavaScript ファイルを *wwwroot* ディレクトリに追加します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-936">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="2a97c-937">その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-937">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="2a97c-938">ローカルで HTML ページをテストするために、ASP.NET Core プロジェクトの起動設定への変更が要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-938">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="2a97c-939">*Properties\launchSettings.json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-939">Open *Properties\launchSettings.json* .</span></span>
* <span data-ttu-id="2a97c-940">アプリが強制的に *index.html*&mdash;プロジェクトの既定ファイルで開くようにするには、`launchUrl` プロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-940">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="2a97c-941">このサンプルでは、Web API のすべての CRUD メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-941">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="2a97c-942">次に、API の呼び出しについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-942">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="2a97c-943">To Do アイテム 2.1 のリストの取得</span><span class="sxs-lookup"><span data-stu-id="2a97c-943">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="2a97c-944">jQuery により HTTP GET 要求が Web API に送信され、API からは To Do アイテムの配列を表す JSON が返されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-944">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="2a97c-945">要求が成功した場合、`success` コールバック関数が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-945">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="2a97c-946">コールバックでは、DOM は To Do 情報で更新されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-946">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="2a97c-947">To Do アイテム 2.1 の追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-947">Add a to-do item 2.1</span></span>

<span data-ttu-id="2a97c-948">jQuery により、要求本文に To Do アイテムが含まれる HTTP POST 要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-948">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="2a97c-949">`accepts` オプションと `contentType` オプションは `application/json` に設定されて、送受信されるメディアの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-949">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="2a97c-950">To Do アイテムは、[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) を使用して JSON に変換されます。</span><span class="sxs-lookup"><span data-stu-id="2a97c-950">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="2a97c-951">API で正常な状況コードが返された場合、`getData` 関数が呼び出され、HTML テーブルを更新します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-951">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="2a97c-952">To Do アイテム 2.1 の更新</span><span class="sxs-lookup"><span data-stu-id="2a97c-952">Update a to-do item 2.1</span></span>

<span data-ttu-id="2a97c-953">To Do アイテムの更新は、追加操作に似ています。</span><span class="sxs-lookup"><span data-stu-id="2a97c-953">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="2a97c-954">アイテムの一意の識別子を追加するように `url` が変更され、`type` は `PUT` となります。</span><span class="sxs-lookup"><span data-stu-id="2a97c-954">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="2a97c-955">To Do アイテム 2.1 の削除</span><span class="sxs-lookup"><span data-stu-id="2a97c-955">Delete a to-do item 2.1</span></span>

<span data-ttu-id="2a97c-956">To Do アイテムを削除するには、`DELETE` への AJAX 呼び出しで `type` を設定して、URL でアイテムの一意の識別子を指定します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-956">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="2a97c-957">Web API 2.1 への認証サポートの追加</span><span class="sxs-lookup"><span data-stu-id="2a97c-957">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="2a97c-958">その他のリソース 2.1</span><span class="sxs-lookup"><span data-stu-id="2a97c-958">Additional resources 2.1</span></span>

<span data-ttu-id="2a97c-959">[このチュートリアルのサンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)します。</span><span class="sxs-lookup"><span data-stu-id="2a97c-959">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="2a97c-960">[ダウンロード方法](xref:index#how-to-download-a-sample)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-960">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="2a97c-961">詳細については、次のリソースを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2a97c-961">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="2a97c-962">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="2a97c-962">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
