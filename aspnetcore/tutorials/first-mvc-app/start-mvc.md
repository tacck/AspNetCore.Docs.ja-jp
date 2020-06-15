---
title: ASP.NET Core MVC の概要
author: rick-anderson
description: ASP.NET Core MVC の概要について説明します。
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c2b76b59ae775b9268fa77019bf8420e5e4108b6
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452290"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="84cd7-103">ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="84cd7-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="84cd7-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="84cd7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="84cd7-105">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="84cd7-106">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="84cd7-107">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="84cd7-108">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="84cd7-108">Create a web app.</span></span>
> * <span data-ttu-id="84cd7-109">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="84cd7-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="84cd7-110">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="84cd7-110">Work with a database.</span></span>
> * <span data-ttu-id="84cd7-111">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="84cd7-111">Add search and validation.</span></span>

<span data-ttu-id="84cd7-112">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="84cd7-113">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84cd7-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84cd7-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84cd7-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="84cd7-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84cd7-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84cd7-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84cd7-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="84cd7-117">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="84cd7-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84cd7-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84cd7-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="84cd7-119">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="84cd7-120">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="84cd7-122">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="84cd7-123">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="84cd7-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)

* <span data-ttu-id="84cd7-125">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="84cd7-126">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="84cd7-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="84cd7-127">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="84cd7-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="84cd7-128">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="84cd7-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="84cd7-129">これは基本的なスターター プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="84cd7-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="84cd7-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84cd7-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="84cd7-131">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="84cd7-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="84cd7-132">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="84cd7-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="84cd7-133">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="84cd7-134">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="84cd7-135">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="84cd7-136">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="84cd7-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="84cd7-137">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="84cd7-137">Select **Yes**</span></span>

  * <span data-ttu-id="84cd7-138">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="84cd7-139">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84cd7-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84cd7-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="84cd7-141">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-141">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="84cd7-143">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="84cd7-144">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="84cd7-146">次の構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="84cd7-147">**[ターゲット フレームワーク]** が **[.NET Core 3.1]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="84cd7-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="84cd7-148">**[認証]** が **[認証なし]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="84cd7-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="84cd7-149">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-149">Select **Next**.</span></span>

  ![macOS .NET Core 3.1 の選択](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="84cd7-151">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="84cd7-153">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="84cd7-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84cd7-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84cd7-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="84cd7-155">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="84cd7-156">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="84cd7-157">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="84cd7-158">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="84cd7-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="84cd7-159">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="84cd7-160">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="84cd7-161">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="84cd7-162">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="84cd7-164">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="84cd7-166">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="84cd7-166">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="84cd7-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84cd7-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="84cd7-169">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="84cd7-170">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="84cd7-171">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="84cd7-172">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="84cd7-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="84cd7-173">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="84cd7-174">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="84cd7-175">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84cd7-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84cd7-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="84cd7-178">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="84cd7-179">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="84cd7-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="84cd7-180">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="84cd7-181">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="84cd7-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="84cd7-182">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="84cd7-183">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="84cd7-184">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="84cd7-185">次の図はアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="84cd7-185">The following image shows the app:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="84cd7-187">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="84cd7-188">次へ</span><span class="sxs-lookup"><span data-stu-id="84cd7-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="84cd7-189">このチュートリアルでは、ASP.NET Core の MVC Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="84cd7-190">このアプリでは、映画タイトルのデータベースを管理します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="84cd7-191">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="84cd7-192">Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="84cd7-192">Create a web app.</span></span>
> * <span data-ttu-id="84cd7-193">モデルを追加してスキャフォールディングする。</span><span class="sxs-lookup"><span data-stu-id="84cd7-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="84cd7-194">データベースを使用する。</span><span class="sxs-lookup"><span data-stu-id="84cd7-194">Work with a database.</span></span>
> * <span data-ttu-id="84cd7-195">検索と検証を追加する。</span><span class="sxs-lookup"><span data-stu-id="84cd7-195">Add search and validation.</span></span>

<span data-ttu-id="84cd7-196">最後には、映画データを管理および表示できるアプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="84cd7-197">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84cd7-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84cd7-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84cd7-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="84cd7-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84cd7-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84cd7-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84cd7-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="84cd7-201">Web アプリの作成</span><span class="sxs-lookup"><span data-stu-id="84cd7-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84cd7-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84cd7-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="84cd7-203">Visual Studio から **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="84cd7-204">**[ASP.NET Core Web アプリケーション]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="84cd7-206">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="84cd7-207">コードをコピーするときに名前空間が一致するように、プロジェクトに **MvcMovie** と名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="84cd7-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](start-mvc/_static/config.png)


* <span data-ttu-id="84cd7-209">**[Web Application(Model-View-Controller)]\(Web アプリケーション (Model-View-Controller)\)** を選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="84cd7-210">[新しいプロジェクト] ダイアログ、左ウィンドウの .NET Core、ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="84cd7-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="84cd7-211">Visual Studio では、作成した MVC プロジェクトに既定のテンプレートが使用されました。</span><span class="sxs-lookup"><span data-stu-id="84cd7-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="84cd7-212">プロジェクト名を入力し、いくつかのオプションを選択すると、すぐに作業アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="84cd7-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="84cd7-213">これは基本的なスターター プロジェクトなので、ここから始めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="84cd7-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="84cd7-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84cd7-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="84cd7-215">このチュートリアルは VS Code の知識があることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="84cd7-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="84cd7-216">詳細については、[VS Code の概要](https://code.visualstudio.com/docs)、および [Visual Studio Code のヘルプ](#visual-studio-code-help)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="84cd7-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="84cd7-217">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="84cd7-218">ディレクトリ (`cd`) を、プロジェクトを格納するフォルダーに変更します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="84cd7-219">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="84cd7-220">"**ビルドとデバッグに必要な資産が 'MvcMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="84cd7-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="84cd7-221">**[はい]** を選択します</span><span class="sxs-lookup"><span data-stu-id="84cd7-221">Select **Yes**</span></span>

  * <span data-ttu-id="84cd7-222">`dotnet new mvc -o MvcMovie`: *MvcMovie* フォルダー内に新しい ASP.NET Core MVC プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="84cd7-223">`code -r MvcMovie`:Visual Studio Code で *MvcMovie.csproj* プロジェクト ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84cd7-224">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84cd7-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="84cd7-225">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-225">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="84cd7-227">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="84cd7-228">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション (Model-View-Controller)]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="84cd7-229">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、 **[.NET Core 2.2]** という既定の **[ターゲット フレームワーク]** を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 の選択](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="84cd7-231">プロジェクトに **MvcMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="84cd7-232">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="84cd7-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84cd7-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84cd7-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="84cd7-234">**Ctrl + F5** キーを押して、デバッグ以外のモードでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="84cd7-235">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="84cd7-236">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="84cd7-237">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="84cd7-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="84cd7-238">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="84cd7-239">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="84cd7-240">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="84cd7-241">**[デバッグ]** メニュー項目から、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![[デバッグ] メニュー](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="84cd7-243">**[IIS Express]** ボタンを選択することで、アプリをデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="84cd7-245">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="84cd7-246">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="84cd7-246">This app doesn't track personal information.</span></span> <span data-ttu-id="84cd7-247">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="84cd7-249">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="84cd7-249">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="84cd7-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84cd7-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="84cd7-252">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="84cd7-253">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="84cd7-254">アドレス バーには、`example.com` などではなく、`localhost:port:5001` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="84cd7-255">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="84cd7-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="84cd7-256">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="84cd7-257">Ctrl + F5 キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="84cd7-258">多くの開発者は、ページを更新して変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="84cd7-259">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="84cd7-260">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="84cd7-260">This app doesn't track personal information.</span></span> <span data-ttu-id="84cd7-261">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/privacy.png)

  <span data-ttu-id="84cd7-263">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="84cd7-263">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84cd7-265">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84cd7-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="84cd7-266">**[実行]**  >  **[デバッグなしで開始]** の順に選択してアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="84cd7-267">Visual Studio for Mac によって [Kestrel](xref:fundamentals/servers/index#kestrel) サーバーが開始され、ブラウザーが起動して `http://localhost:port` にアクセスします。*port* はランダムに選択されたポート番号になります。</span><span class="sxs-lookup"><span data-stu-id="84cd7-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="84cd7-268">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="84cd7-269">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="84cd7-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="84cd7-270">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="84cd7-271">アプリを実行する際には、別のポート番号が表示されます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="84cd7-272">**[実行]** メニューから、デバッグ モードまたは非デバッグ モードでアプリを起動できます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="84cd7-273">**[同意する]** を選択し、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="84cd7-274">このアプリでは個人情報は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="84cd7-274">This app doesn't track personal information.</span></span> <span data-ttu-id="84cd7-275">テンプレートで生成されたコードには、[一般的なデータ保護規制 (GDPR)](xref:security/gdpr) を満たす資産が含まれます。</span><span class="sxs-lookup"><span data-stu-id="84cd7-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="84cd7-277">次の図は、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="84cd7-277">The following image shows the app after accepting tracking:</span></span>

  ![ホームまたはインデックス ページ](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="84cd7-279">このチュートリアルの次のパートでは、MVC について説明し、コードの作成を開始します。</span><span class="sxs-lookup"><span data-stu-id="84cd7-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="84cd7-280">次へ</span><span class="sxs-lookup"><span data-stu-id="84cd7-280">Next</span></span>](adding-controller.md)

::: moniker-end
