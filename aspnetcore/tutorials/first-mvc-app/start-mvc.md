---
title: ASP.NET Core MVC の概要
author: rick-anderson
description: ASP.NET Core MVC の概要について説明します。
ms.author: riande
ms.date: 10/16/2019
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
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 9d70b292a93a5d19cc25b2fc592ec88ce8262434
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629991"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="049ad-103">ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="049ad-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="049ad-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="049ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="049ad-105">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="049ad-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="049ad-106">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="049ad-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="049ad-107">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="049ad-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="049ad-108">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="049ad-108">Create a web app.</span></span>
> * <span data-ttu-id="049ad-109">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="049ad-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="049ad-110">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="049ad-110">Work with a database.</span></span>
> * <span data-ttu-id="049ad-111">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="049ad-111">Add search and validation.</span></span>

<span data-ttu-id="049ad-112">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="049ad-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="049ad-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="049ad-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="049ad-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="049ad-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="049ad-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="049ad-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="049ad-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="049ad-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="049ad-117">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="049ad-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="049ad-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="049ad-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="049ad-119">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="049ad-120">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="049ad-122">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="049ad-123">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="049ad-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)

* <span data-ttu-id="049ad-125">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="049ad-126">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="049ad-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="049ad-127">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="049ad-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="049ad-128">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="049ad-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="049ad-129">これは基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="049ad-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="049ad-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="049ad-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="049ad-131">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="049ad-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="049ad-132">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="049ad-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="049ad-133">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="049ad-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="049ad-134">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="049ad-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="049ad-135">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="049ad-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="049ad-136">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="049ad-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="049ad-137">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="049ad-137">Select **Yes**</span></span>

  * <span data-ttu-id="049ad-138">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="049ad-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="049ad-139">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="049ad-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="049ad-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="049ad-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="049ad-141">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-141">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="049ad-143">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="049ad-144">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="049ad-146">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="049ad-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="049ad-147">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="049ad-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="049ad-148">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 3.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="049ad-149">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-149">Select **Next**.</span></span>

* <span data-ttu-id="049ad-150">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="049ad-152">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="049ad-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="049ad-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="049ad-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="049ad-154">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="049ad-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="049ad-155">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="049ad-156">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="049ad-157">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="049ad-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="049ad-158">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="049ad-159">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="049ad-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="049ad-160">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="049ad-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="049ad-161">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="049ad-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="049ad-163">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="049ad-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="049ad-165">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="049ad-165">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="049ad-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="049ad-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="049ad-168">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="049ad-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="049ad-169">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="049ad-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="049ad-170">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="049ad-171">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="049ad-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="049ad-172">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="049ad-173">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="049ad-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="049ad-174">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="049ad-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="049ad-176">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="049ad-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="049ad-177">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="049ad-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="049ad-178">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="049ad-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="049ad-179">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="049ad-180">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="049ad-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="049ad-181">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="049ad-182">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="049ad-183">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="049ad-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="049ad-184">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="049ad-184">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="049ad-186">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="049ad-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="049ad-187">次へ</span><span class="sxs-lookup"><span data-stu-id="049ad-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="049ad-188">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="049ad-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="049ad-189">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="049ad-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="049ad-190">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="049ad-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="049ad-191">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="049ad-191">Create a web app.</span></span>
> * <span data-ttu-id="049ad-192">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="049ad-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="049ad-193">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="049ad-193">Work with a database.</span></span>
> * <span data-ttu-id="049ad-194">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="049ad-194">Add search and validation.</span></span>

<span data-ttu-id="049ad-195">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="049ad-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="049ad-196">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="049ad-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="049ad-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="049ad-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="049ad-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="049ad-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="049ad-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="049ad-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="049ad-200">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="049ad-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="049ad-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="049ad-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="049ad-202">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="049ad-203">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="049ad-205">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="049ad-206">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="049ad-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)


* <span data-ttu-id="049ad-208">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="049ad-209">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="049ad-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="049ad-210">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="049ad-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="049ad-211">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="049ad-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="049ad-212">これは基本的なスターター プロジェクトなので、ここから始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="049ad-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="049ad-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="049ad-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="049ad-214">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="049ad-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="049ad-215">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="049ad-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="049ad-216">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="049ad-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="049ad-217">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="049ad-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="049ad-218">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="049ad-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="049ad-219">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="049ad-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="049ad-220">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="049ad-220">Select **Yes**</span></span>

  * <span data-ttu-id="049ad-221">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="049ad-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="049ad-222">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="049ad-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="049ad-223">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="049ad-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="049ad-224">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-224">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="049ad-226">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="049ad-227">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="049ad-228">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="049ad-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="049ad-229">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="049ad-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="049ad-230">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 2.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="049ad-231">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-231">Select **Next**.</span></span>

* <span data-ttu-id="049ad-232">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="049ad-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="049ad-233">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="049ad-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="049ad-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="049ad-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="049ad-235">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="049ad-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="049ad-236">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="049ad-237">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="049ad-238">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="049ad-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="049ad-239">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="049ad-240">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="049ad-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="049ad-241">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="049ad-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="049ad-242">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="049ad-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="049ad-244">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="049ad-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="049ad-246">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="049ad-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="049ad-247">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="049ad-247">This app doesn't track personal information.</span></span> <span data-ttu-id="049ad-248">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="049ad-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="049ad-250">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="049ad-250">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="049ad-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="049ad-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="049ad-253">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="049ad-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="049ad-254">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="049ad-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="049ad-255">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="049ad-256">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="049ad-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="049ad-257">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="049ad-258">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="049ad-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="049ad-259">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="049ad-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="049ad-260">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="049ad-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="049ad-261">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="049ad-261">This app doesn't track personal information.</span></span> <span data-ttu-id="049ad-262">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="049ad-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="049ad-264">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="049ad-264">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="049ad-266">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="049ad-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="049ad-267">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="049ad-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="049ad-268">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="049ad-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="049ad-269">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="049ad-270">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="049ad-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="049ad-271">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="049ad-272">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="049ad-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="049ad-273">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="049ad-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="049ad-274">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="049ad-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="049ad-275">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="049ad-275">This app doesn't track personal information.</span></span> <span data-ttu-id="049ad-276">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="049ad-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="049ad-278">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="049ad-278">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="049ad-280">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="049ad-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="049ad-281">次へ</span><span class="sxs-lookup"><span data-stu-id="049ad-281">Next</span></span>](adding-controller.md)

::: moniker-end
