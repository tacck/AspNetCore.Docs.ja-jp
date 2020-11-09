---
title: ASP.NET Core MVC の概要
author: rick-anderson
description: ASP.NET Core MVC の概要について説明します。
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: cf17aaf8eff342c378536d4f635e09b936459bee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052904"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="69190-103">ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="69190-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="69190-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="69190-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="69190-105">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="69190-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="69190-106">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="69190-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="69190-107">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="69190-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="69190-108">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="69190-108">Create a web app.</span></span>
> * <span data-ttu-id="69190-109">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="69190-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="69190-110">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="69190-110">Work with a database.</span></span>
> * <span data-ttu-id="69190-111">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="69190-111">Add search and validation.</span></span>

<span data-ttu-id="69190-112">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="69190-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="69190-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="69190-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69190-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69190-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="69190-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="69190-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="69190-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="69190-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="69190-117">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="69190-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69190-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69190-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="69190-119">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="69190-120">**[ASP.NET Core Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-120">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="69190-122">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="69190-123">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="69190-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)

* <span data-ttu-id="69190-125">**[Web アプリケーション (モデル ビュー コントローラー)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-125">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="69190-126">ドロップダウン ボックスから **[.NET Core]** と **[ASP.NET Core 3.1]** を選択した後、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create**.</span></span>

![<span data-ttu-id="69190-127">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="69190-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="69190-128">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="69190-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="69190-129">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="69190-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="69190-130">これは基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="69190-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="69190-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="69190-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="69190-132">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="69190-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="69190-133">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69190-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="69190-134">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="69190-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="69190-135">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="69190-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="69190-136">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="69190-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="69190-137">" **ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="69190-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="69190-138">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="69190-138">Select **Yes**</span></span>

  * <span data-ttu-id="69190-139">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="69190-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="69190-140">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="69190-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="69190-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="69190-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="69190-142">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-142">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="69190-144">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="69190-145">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="69190-147">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="69190-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="69190-148">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69190-148">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="69190-149">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 3.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-149">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="69190-150">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-150">Select **Next**.</span></span>

* <span data-ttu-id="69190-151">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-151">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="69190-153">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="69190-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69190-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69190-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="69190-155">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="69190-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="69190-156">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="69190-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="69190-157">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="69190-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="69190-158">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="69190-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="69190-159">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="69190-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="69190-160">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="69190-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="69190-161">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="69190-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="69190-162">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="69190-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="69190-164">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="69190-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="69190-166">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="69190-166">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="69190-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="69190-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="69190-169">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="69190-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="69190-170">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="69190-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="69190-171">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="69190-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="69190-172">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="69190-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="69190-173">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="69190-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="69190-174">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="69190-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="69190-175">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="69190-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="69190-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="69190-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="69190-178">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="69190-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="69190-179">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。 *port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="69190-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="69190-180">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="69190-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="69190-181">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="69190-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="69190-182">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="69190-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="69190-183">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="69190-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="69190-184">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="69190-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="69190-185">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="69190-185">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="69190-187">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="69190-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="69190-188">次へ</span><span class="sxs-lookup"><span data-stu-id="69190-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="69190-189">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="69190-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="69190-190">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="69190-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="69190-191">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="69190-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="69190-192">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="69190-192">Create a web app.</span></span>
> * <span data-ttu-id="69190-193">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="69190-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="69190-194">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="69190-194">Work with a database.</span></span>
> * <span data-ttu-id="69190-195">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="69190-195">Add search and validation.</span></span>

<span data-ttu-id="69190-196">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="69190-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="69190-197">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="69190-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69190-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69190-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="69190-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="69190-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="69190-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="69190-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="69190-201">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="69190-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69190-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69190-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="69190-203">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="69190-204">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="69190-206">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="69190-207">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="69190-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)


* <span data-ttu-id="69190-209">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-209">Select **Web Application(Model-View-Controller)** , and then select **Create**.</span></span>

![<span data-ttu-id="69190-210">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="69190-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="69190-211">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="69190-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="69190-212">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="69190-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="69190-213">これは基本的なスターター プロジェクトなので、ここから始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="69190-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="69190-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="69190-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="69190-215">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="69190-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="69190-216">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="69190-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="69190-217">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="69190-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="69190-218">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="69190-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="69190-219">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="69190-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="69190-220">" **ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="69190-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="69190-221">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="69190-221">Select **Yes**</span></span>

  * <span data-ttu-id="69190-222">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="69190-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="69190-223">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="69190-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="69190-224">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="69190-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="69190-225">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-225">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="69190-227">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="69190-228">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="69190-229">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="69190-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="69190-230">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="69190-230">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="69190-231">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 2.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-231">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="69190-232">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-232">Select **Next**.</span></span>

* <span data-ttu-id="69190-233">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="69190-233">Name the project **MvcMovie** , and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="69190-234">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="69190-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="69190-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="69190-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="69190-236">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="69190-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="69190-237">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="69190-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="69190-238">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="69190-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="69190-239">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="69190-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="69190-240">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="69190-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="69190-241">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="69190-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="69190-242">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="69190-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="69190-243">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="69190-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="69190-245">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="69190-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="69190-247">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="69190-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="69190-248">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="69190-248">This app doesn't track personal information.</span></span> <span data-ttu-id="69190-249">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="69190-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="69190-251">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="69190-251">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="69190-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="69190-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="69190-254">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="69190-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="69190-255">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="69190-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="69190-256">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="69190-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="69190-257">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="69190-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="69190-258">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="69190-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="69190-259">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="69190-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="69190-260">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="69190-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="69190-261">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="69190-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="69190-262">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="69190-262">This app doesn't track personal information.</span></span> <span data-ttu-id="69190-263">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="69190-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="69190-265">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="69190-265">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="69190-267">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="69190-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="69190-268">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="69190-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="69190-269">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。 *port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="69190-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="69190-270">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="69190-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="69190-271">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="69190-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="69190-272">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="69190-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="69190-273">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="69190-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="69190-274">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="69190-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="69190-275">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="69190-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="69190-276">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="69190-276">This app doesn't track personal information.</span></span> <span data-ttu-id="69190-277">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="69190-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="69190-279">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="69190-279">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="69190-281">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="69190-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="69190-282">次へ</span><span class="sxs-lookup"><span data-stu-id="69190-282">Next</span></span>](adding-controller.md)

::: moniker-end
