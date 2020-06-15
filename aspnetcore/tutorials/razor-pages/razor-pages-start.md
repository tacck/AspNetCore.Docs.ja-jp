---
title: "\"チュートリアル: ASP.NET Core の Razor Pages の概要\""
author: rick-anderson
description: このチュートリアル シリーズでは、ASP.NET Core で Razor ページを使用する方法を示します。 モデルの作成、Razor ページのコードの生成、Entity Framework Core と SQL Server を使用したデータ アクセス、検索機能の追加、入力検証の追加、移行を使用したモデルの更新の方法について学習します。
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 3b8ccf639bb91234f81c67750fffa170e52d636f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452347"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="58276-104">チュートリアル: ASP.NET Core の Razor Pages の概要</span><span class="sxs-lookup"><span data-stu-id="58276-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="58276-105">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58276-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="58276-106">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="58276-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="58276-107">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="58276-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="58276-108">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="58276-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="58276-109">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="58276-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="58276-110">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="58276-110">Run the app.</span></span>
> * <span data-ttu-id="58276-111">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="58276-111">Examine the project files.</span></span>

<span data-ttu-id="58276-112">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="58276-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="58276-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="58276-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58276-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58276-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="58276-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58276-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58276-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58276-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="58276-118">Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="58276-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58276-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58276-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58276-120">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="58276-121">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="58276-122">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="58276-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="58276-123">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="58276-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="58276-124">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="58276-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="58276-125">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="58276-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="58276-126">ドロップダウンの **[ASP.NET Core 3.1]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="58276-128">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="58276-128">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="58276-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58276-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="58276-131">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="58276-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="58276-132">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="58276-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="58276-133">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="58276-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="58276-134">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="58276-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="58276-135">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="58276-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="58276-136">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="58276-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="58276-137">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-137">Select **Yes**.</span></span>

  <span data-ttu-id="58276-138">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="58276-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58276-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58276-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58276-140">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-140">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="58276-142">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="58276-143">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="58276-145">次の構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="58276-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="58276-146">**[ターゲット フレームワーク]** が **[.NET Core 3.1]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="58276-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="58276-147">**[認証]** が **[認証なし]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="58276-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="58276-148">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-148">Select **Next**.</span></span>

  ![macOS .NET Core 3.1 の選択](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="58276-150">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="58276-152">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="58276-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="58276-153">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="58276-153">Examine the project files</span></span>

<span data-ttu-id="58276-154">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="58276-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="58276-155">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="58276-155">Pages folder</span></span>

<span data-ttu-id="58276-156">Razor ページとサポート ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="58276-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="58276-157">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="58276-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="58276-158">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが含まれます。</span><span class="sxs-lookup"><span data-stu-id="58276-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="58276-159">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="58276-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="58276-160">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="58276-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="58276-161">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="58276-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="58276-162">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="58276-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="58276-163">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="58276-164">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="58276-164">wwwroot folder</span></span>

<span data-ttu-id="58276-165">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="58276-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="58276-166">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="58276-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="58276-167">appSettings.json</span></span>

<span data-ttu-id="58276-168">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="58276-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="58276-169">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="58276-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="58276-170">Program.cs</span></span>

<span data-ttu-id="58276-171">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="58276-171">Contains the entry point for the program.</span></span> <span data-ttu-id="58276-172">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="58276-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="58276-173">Startup.cs</span></span>

<span data-ttu-id="58276-174">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="58276-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="58276-175">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="58276-176">次の手順</span><span class="sxs-lookup"><span data-stu-id="58276-176">Next steps</span></span>

<span data-ttu-id="58276-177">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="58276-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="58276-178">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="58276-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58276-179">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="58276-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="58276-180">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="58276-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="58276-181">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="58276-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="58276-182">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="58276-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="58276-183">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="58276-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="58276-184">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="58276-184">Run the app.</span></span>
> * <span data-ttu-id="58276-185">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="58276-185">Examine the project files.</span></span>

<span data-ttu-id="58276-186">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="58276-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="58276-188">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="58276-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58276-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58276-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="58276-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58276-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58276-191">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58276-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="58276-192">Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="58276-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58276-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58276-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58276-194">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="58276-195">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="58276-197">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="58276-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="58276-198">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="58276-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="58276-200">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="58276-202">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="58276-202">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="58276-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58276-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="58276-205">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="58276-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="58276-206">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="58276-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="58276-207">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="58276-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="58276-208">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="58276-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="58276-209">`code` コマンドは、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="58276-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="58276-210">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="58276-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="58276-211">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-211">Select **Yes**.</span></span>

  <span data-ttu-id="58276-212">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="58276-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58276-213">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58276-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58276-214">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-214">Select **File** > **New Solution**.</span></span>

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="58276-216">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="58276-217">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="58276-218">**[Configure your new ASP.NET Core Web API]\(新しい ASP.NET Core Web API を構成する\)** ダイアログで、 **[ターゲット フレームワーク]** を **[.NET Core 3.1]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="58276-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.0 の選択](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="58276-220">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="58276-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="58276-222">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="58276-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58276-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58276-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58276-224">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="58276-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="58276-225">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="58276-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="58276-226">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="58276-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="58276-227">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="58276-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="58276-228">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="58276-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="58276-229">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="58276-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="58276-230">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="58276-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="58276-231">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="58276-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="58276-233">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="58276-233">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="58276-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58276-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="58276-236">**Ctrl + F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="58276-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="58276-237">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="58276-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="58276-238">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="58276-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="58276-239">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="58276-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="58276-240">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="58276-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="58276-241">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="58276-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="58276-242">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="58276-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="58276-244">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="58276-244">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58276-246">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58276-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="58276-247">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="58276-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="58276-248">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="58276-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="58276-249">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="58276-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="58276-250">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="58276-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="58276-252">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="58276-252">The following image shows the app after you give consent to tracking:</span></span>

  ![ホームまたはインデックス ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="58276-254">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="58276-254">Examine the project files</span></span>

<span data-ttu-id="58276-255">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="58276-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="58276-256">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="58276-256">Pages folder</span></span>

<span data-ttu-id="58276-257">Razor ページとサポート ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="58276-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="58276-258">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="58276-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="58276-259">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが含まれます。</span><span class="sxs-lookup"><span data-stu-id="58276-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="58276-260">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="58276-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="58276-261">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="58276-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="58276-262">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="58276-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="58276-263">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="58276-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="58276-264">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="58276-265">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="58276-265">wwwroot folder</span></span>

<span data-ttu-id="58276-266">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが格納されます。</span><span class="sxs-lookup"><span data-stu-id="58276-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="58276-267">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="58276-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="58276-268">appSettings.json</span></span>

<span data-ttu-id="58276-269">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="58276-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="58276-270">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="58276-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="58276-271">Program.cs</span></span>

<span data-ttu-id="58276-272">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="58276-272">Contains the entry point for the program.</span></span> <span data-ttu-id="58276-273">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="58276-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="58276-274">Startup.cs</span></span>

<span data-ttu-id="58276-275">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="58276-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="58276-276">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="58276-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58276-277">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="58276-277">Additional resources</span></span>

* [<span data-ttu-id="58276-278">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="58276-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="58276-279">次の手順</span><span class="sxs-lookup"><span data-stu-id="58276-279">Next steps</span></span>

<span data-ttu-id="58276-280">このシリーズの次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="58276-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="58276-281">モデルの追加</span><span class="sxs-lookup"><span data-stu-id="58276-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
