---
title: ASP.NET Core MVC の概要
author: rick-anderson
description: ASP.NET Core MVC の概要について説明します。
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688529"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="ef20b-103">ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="ef20b-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="ef20b-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ef20b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="ef20b-105">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="ef20b-106">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="ef20b-107">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ef20b-108">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-108">Create a web app.</span></span>
> * <span data-ttu-id="ef20b-109">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="ef20b-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="ef20b-110">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-110">Work with a database.</span></span>
> * <span data-ttu-id="ef20b-111">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-111">Add search and validation.</span></span>

<span data-ttu-id="ef20b-112">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="ef20b-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ef20b-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="ef20b-117">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="ef20b-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ef20b-119">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="ef20b-120">**[新しいプロジェクトの作成]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="ef20b-121">**[新しいプロジェクトの構成]** ダイアログで、 **[プロジェクト名]** に「`MvcMovie`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="ef20b-122">コードをコピーするときに各`namespace`が一致するように、この正確な名前 (大文字と小文字を含む) を使用することが重要です。</span><span class="sxs-lookup"><span data-stu-id="ef20b-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="ef20b-123">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="ef20b-123">Select **Create**.</span></span>
1. <span data-ttu-id="ef20b-124">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで、次のものを選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="ef20b-125">ドロップダウンで **[.NET Core]** と **[ASP.NET Core 5.0]**</span><span class="sxs-lookup"><span data-stu-id="ef20b-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="ef20b-126">**ASP.NET Core Web アプリ (Model-View-Controller)**</span><span class="sxs-lookup"><span data-stu-id="ef20b-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="ef20b-127">**作成**</span><span class="sxs-lookup"><span data-stu-id="ef20b-127">**Create**</span></span>

![<span data-ttu-id="ef20b-128">新しい ASP.NET Core Web アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="ef20b-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="ef20b-129">プロジェクトを作成する別の方法については、「[Visual Studio で新しいプロジェクトを作成する](/visualstudio/ide/create-new-project)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ef20b-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="ef20b-130">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="ef20b-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="ef20b-131">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="ef20b-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="ef20b-132">これは基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ef20b-134">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="ef20b-135">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ef20b-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="ef20b-136">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ef20b-137">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ef20b-138">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="ef20b-139">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="ef20b-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="ef20b-140">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="ef20b-140">Select **Yes**</span></span>

  * <span data-ttu-id="ef20b-141">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="ef20b-142">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-143">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef20b-144">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-144">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ef20b-146">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="ef20b-147">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="ef20b-149">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="ef20b-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="ef20b-150">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="ef20b-151">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 5.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="ef20b-152">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-152">Select **Next**.</span></span>

* <span data-ttu-id="ef20b-153">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="ef20b-155">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="ef20b-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef20b-157">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="ef20b-158">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ef20b-159">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-160">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ef20b-161">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="ef20b-162">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ef20b-163">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="ef20b-164">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="ef20b-166">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="ef20b-168">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-168">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ef20b-171">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="ef20b-172">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="ef20b-173">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-174">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="ef20b-175">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="ef20b-176">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ef20b-177">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ef20b-180">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="ef20b-181">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="ef20b-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ef20b-182">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-183">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ef20b-184">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="ef20b-185">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="ef20b-186">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="ef20b-187">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-187">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="ef20b-189">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ef20b-190">次へ</span><span class="sxs-lookup"><span data-stu-id="ef20b-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="ef20b-191">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="ef20b-192">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="ef20b-193">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ef20b-194">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-194">Create a web app.</span></span>
> * <span data-ttu-id="ef20b-195">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="ef20b-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="ef20b-196">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-196">Work with a database.</span></span>
> * <span data-ttu-id="ef20b-197">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-197">Add search and validation.</span></span>

<span data-ttu-id="ef20b-198">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="ef20b-199">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ef20b-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="ef20b-203">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="ef20b-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef20b-205">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="ef20b-206">**[ASP.NET Core Web アプリケーション]** > **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="ef20b-208">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="ef20b-209">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="ef20b-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)

* <span data-ttu-id="ef20b-211">**[Web アプリケーション (モデル ビュー コントローラー)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="ef20b-212">ドロップダウン ボックスから **[.NET Core]** と **[ASP.NET Core 3.1]** を選択した後、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="ef20b-213">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="ef20b-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="ef20b-214">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="ef20b-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="ef20b-215">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="ef20b-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="ef20b-216">これは基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ef20b-218">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="ef20b-219">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ef20b-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="ef20b-220">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ef20b-221">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ef20b-222">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="ef20b-223">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="ef20b-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="ef20b-224">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="ef20b-224">Select **Yes**</span></span>

  * <span data-ttu-id="ef20b-225">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="ef20b-226">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef20b-228">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-228">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ef20b-230">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="ef20b-231">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="ef20b-233">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="ef20b-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="ef20b-234">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="ef20b-235">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 3.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="ef20b-236">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-236">Select **Next**.</span></span>

* <span data-ttu-id="ef20b-237">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="ef20b-239">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="ef20b-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef20b-241">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="ef20b-242">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ef20b-243">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-244">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ef20b-245">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="ef20b-246">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ef20b-247">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="ef20b-248">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="ef20b-250">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="ef20b-252">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-252">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ef20b-255">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="ef20b-256">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="ef20b-257">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-258">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="ef20b-259">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="ef20b-260">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ef20b-261">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-263">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ef20b-264">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="ef20b-265">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="ef20b-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ef20b-266">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-267">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ef20b-268">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="ef20b-269">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="ef20b-270">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="ef20b-271">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-271">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="ef20b-273">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ef20b-274">次へ</span><span class="sxs-lookup"><span data-stu-id="ef20b-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="ef20b-275">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="ef20b-276">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="ef20b-277">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ef20b-278">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-278">Create a web app.</span></span>
> * <span data-ttu-id="ef20b-279">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="ef20b-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="ef20b-280">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-280">Work with a database.</span></span>
> * <span data-ttu-id="ef20b-281">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="ef20b-281">Add search and validation.</span></span>

<span data-ttu-id="ef20b-282">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="ef20b-283">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ef20b-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-286">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="ef20b-287">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="ef20b-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ef20b-289">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="ef20b-290">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="ef20b-292">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="ef20b-293">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="ef20b-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)


* <span data-ttu-id="ef20b-295">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="ef20b-296">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="ef20b-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="ef20b-297">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="ef20b-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="ef20b-298">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="ef20b-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="ef20b-299">これは基本的なスターター プロジェクトなので、ここから始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ef20b-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ef20b-301">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="ef20b-302">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ef20b-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="ef20b-303">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="ef20b-304">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="ef20b-305">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="ef20b-306">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="ef20b-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="ef20b-307">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="ef20b-307">Select **Yes**</span></span>

  * <span data-ttu-id="ef20b-308">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="ef20b-309">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-310">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="ef20b-311">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-311">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="ef20b-313">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="ef20b-314">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="ef20b-315">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="ef20b-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="ef20b-316">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="ef20b-317">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 2.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="ef20b-318">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-318">Select **Next**.</span></span>

* <span data-ttu-id="ef20b-319">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="ef20b-320">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="ef20b-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ef20b-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ef20b-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ef20b-322">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="ef20b-323">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="ef20b-324">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-325">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ef20b-326">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="ef20b-327">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ef20b-328">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="ef20b-329">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="ef20b-331">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="ef20b-333">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="ef20b-334">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="ef20b-334">This app doesn't track personal information.</span></span> <span data-ttu-id="ef20b-335">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="ef20b-337">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-337">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ef20b-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ef20b-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ef20b-340">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="ef20b-341">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="ef20b-342">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-343">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="ef20b-344">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="ef20b-345">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="ef20b-346">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="ef20b-347">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="ef20b-348">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="ef20b-348">This app doesn't track personal information.</span></span> <span data-ttu-id="ef20b-349">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="ef20b-351">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-351">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ef20b-353">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ef20b-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ef20b-354">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="ef20b-355">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="ef20b-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="ef20b-356">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="ef20b-357">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="ef20b-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="ef20b-358">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="ef20b-359">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="ef20b-360">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="ef20b-361">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="ef20b-362">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="ef20b-362">This app doesn't track personal information.</span></span> <span data-ttu-id="ef20b-363">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ef20b-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="ef20b-365">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="ef20b-365">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="ef20b-367">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="ef20b-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ef20b-368">次へ</span><span class="sxs-lookup"><span data-stu-id="ef20b-368">Next</span></span>](adding-controller.md)

::: moniker-end
