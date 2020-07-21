---
title: 'チュートリアル: ASP.NET Core の Razor Pages の概要'
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、移行を使用したモデルの更新の方法について学習します。
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 77b230f14b4eef60d771daf8fe09288a9dd3c69c
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212991"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="56af5-104">チュートリアル: ASP.NET Core の Razor Pages の概要</span><span class="sxs-lookup"><span data-stu-id="56af5-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="56af5-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="56af5-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="56af5-106">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="56af5-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="56af5-107">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="56af5-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="56af5-108">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="56af5-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="56af5-109">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="56af5-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="56af5-110">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="56af5-110">Run the app.</span></span>
> * <span data-ttu-id="56af5-111">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="56af5-111">Examine the project files.</span></span>

<span data-ttu-id="56af5-112">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="56af5-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="56af5-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="56af5-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56af5-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56af5-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="56af5-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56af5-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56af5-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="56af5-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="56af5-118">Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="56af5-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56af5-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56af5-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56af5-120">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="56af5-121">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="56af5-122">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="56af5-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="56af5-123">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="56af5-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="56af5-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="56af5-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="56af5-125">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="56af5-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="56af5-126">ドロップダウンの **[ASP.NET Core 3.1]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="56af5-128">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-128">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="56af5-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56af5-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="56af5-131">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="56af5-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="56af5-132">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="56af5-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="56af5-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="56af5-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="56af5-134">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="56af5-135">`code` コマンドを実行すると、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーが開きます。</span><span class="sxs-lookup"><span data-stu-id="56af5-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="56af5-136">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="56af5-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="56af5-137">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-137">Select **Yes**.</span></span>

  <span data-ttu-id="56af5-138">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56af5-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="56af5-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="56af5-140">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-140">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="56af5-142">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="56af5-143">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="56af5-145">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="56af5-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="56af5-146">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="56af5-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="56af5-147">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 3.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-147">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="56af5-148">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-148">Select **Next**.</span></span>

* <span data-ttu-id="56af5-149">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-149">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="56af5-151">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="56af5-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="56af5-152">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="56af5-152">Examine the project files</span></span>

<span data-ttu-id="56af5-153">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="56af5-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="56af5-154">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="56af5-154">Pages folder</span></span>

<span data-ttu-id="56af5-155">Razor ページとサポート ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="56af5-155">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="56af5-156">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="56af5-156">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="56af5-157">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが含まれます。</span><span class="sxs-lookup"><span data-stu-id="56af5-157">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="56af5-158">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="56af5-159">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="56af5-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="56af5-160">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="56af5-161">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="56af5-162">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="56af5-163">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="56af5-163">wwwroot folder</span></span>

<span data-ttu-id="56af5-164">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="56af5-165">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="56af5-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="56af5-166">appSettings.json</span></span>

<span data-ttu-id="56af5-167">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="56af5-168">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="56af5-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="56af5-169">Program.cs</span></span>

<span data-ttu-id="56af5-170">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-170">Contains the entry point for the program.</span></span> <span data-ttu-id="56af5-171">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="56af5-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="56af5-172">Startup.cs</span></span>

<span data-ttu-id="56af5-173">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="56af5-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="56af5-174">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="56af5-175">次の手順</span><span class="sxs-lookup"><span data-stu-id="56af5-175">Next steps</span></span>

<span data-ttu-id="56af5-176">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="56af5-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="56af5-177">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="56af5-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="56af5-178">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="56af5-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="56af5-179">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="56af5-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="56af5-180">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="56af5-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="56af5-181">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="56af5-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="56af5-182">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="56af5-182">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="56af5-183">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="56af5-183">Run the app.</span></span>
> * <span data-ttu-id="56af5-184">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="56af5-184">Examine the project files.</span></span>

<span data-ttu-id="56af5-185">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="56af5-185">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="56af5-187">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="56af5-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56af5-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56af5-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="56af5-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56af5-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56af5-190">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="56af5-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="56af5-191">Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="56af5-191">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56af5-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56af5-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56af5-193">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="56af5-194">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="56af5-196">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="56af5-196">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="56af5-197">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="56af5-197">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="56af5-199">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="56af5-201">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-201">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="56af5-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56af5-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="56af5-204">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="56af5-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="56af5-205">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="56af5-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="56af5-206">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="56af5-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="56af5-207">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-207">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="56af5-208">`code` コマンドを実行すると、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーが開きます。</span><span class="sxs-lookup"><span data-stu-id="56af5-208">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="56af5-209">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="56af5-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="56af5-210">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-210">Select **Yes**.</span></span>

  <span data-ttu-id="56af5-211">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56af5-212">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="56af5-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="56af5-213">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-213">Select **File** > **New Solution**.</span></span>

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="56af5-215">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="56af5-216">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="56af5-217">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="56af5-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="56af5-218">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="56af5-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="56af5-219">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 2.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-219">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="56af5-220">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-220">Select **Next**.</span></span>

* <span data-ttu-id="56af5-221">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="56af5-221">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="56af5-223">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="56af5-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56af5-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56af5-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56af5-225">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="56af5-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="56af5-226">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="56af5-227">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="56af5-228">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="56af5-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="56af5-229">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="56af5-230">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="56af5-231">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="56af5-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="56af5-232">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="56af5-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="56af5-234">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="56af5-234">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="56af5-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56af5-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="56af5-237">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="56af5-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="56af5-238">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="56af5-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="56af5-239">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="56af5-240">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="56af5-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="56af5-241">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="56af5-242">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="56af5-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="56af5-243">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="56af5-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="56af5-245">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="56af5-245">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56af5-247">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="56af5-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="56af5-248">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="56af5-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="56af5-249">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="56af5-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="56af5-250">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="56af5-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="56af5-251">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="56af5-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="56af5-253">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="56af5-253">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="56af5-255">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="56af5-255">Examine the project files</span></span>

<span data-ttu-id="56af5-256">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="56af5-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="56af5-257">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="56af5-257">Pages folder</span></span>

<span data-ttu-id="56af5-258">Razor ページとサポート ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="56af5-258">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="56af5-259">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="56af5-259">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="56af5-260">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが含まれます。</span><span class="sxs-lookup"><span data-stu-id="56af5-260">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="56af5-261">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="56af5-262">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="56af5-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="56af5-263">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="56af5-264">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="56af5-265">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="56af5-266">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="56af5-266">wwwroot folder</span></span>

<span data-ttu-id="56af5-267">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="56af5-268">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="56af5-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="56af5-269">appSettings.json</span></span>

<span data-ttu-id="56af5-270">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="56af5-271">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="56af5-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="56af5-272">Program.cs</span></span>

<span data-ttu-id="56af5-273">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-273">Contains the entry point for the program.</span></span> <span data-ttu-id="56af5-274">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="56af5-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="56af5-275">Startup.cs</span></span>

<span data-ttu-id="56af5-276">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="56af5-276">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="56af5-277">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="56af5-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="56af5-278">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="56af5-278">Additional resources</span></span>

* [<span data-ttu-id="56af5-279">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="56af5-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="56af5-280">次の手順</span><span class="sxs-lookup"><span data-stu-id="56af5-280">Next steps</span></span>

<span data-ttu-id="56af5-281">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="56af5-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="56af5-282">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="56af5-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
