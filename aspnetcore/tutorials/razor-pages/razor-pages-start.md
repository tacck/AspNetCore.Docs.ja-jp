---
title: 'チュートリアル: ASP.NET Core の Razor Pages の概要'
author: rick-anderson
description: これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。
ms.author: riande
ms.date: 09/15/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4d4e50f8acea73859f5e839616f13f90a42291c4
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486227"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="8af86-103">チュートリアル: ASP.NET Core の Razor Pages の概要</span><span class="sxs-lookup"><span data-stu-id="8af86-103">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="8af86-104">作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8af86-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="8af86-105">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="8af86-105">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="8af86-106">コントローラーとビューを使い慣れた開発者向けの詳細情報については、「[Razor Pages の概要](xref:razor-pages/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-106">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="8af86-107">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="8af86-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="8af86-108">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8af86-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8af86-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="8af86-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8af86-110">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="8af86-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="8af86-111">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-111">Run the app.</span></span>
> * <span data-ttu-id="8af86-112">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="8af86-112">Examine the project files.</span></span>

<span data-ttu-id="8af86-113">このチュートリアルの最後には、以降のチュートリアルで拡張する作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="8af86-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll enhance in later tutorials.</span></span>

![[ホーム] または [Index] ページ](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a><span data-ttu-id="8af86-115">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8af86-115">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af86-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af86-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af86-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af86-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af86-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8af86-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="8af86-119">Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="8af86-119">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af86-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af86-120">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8af86-121">Visual Studio を開始し、 **[新しいプロジェクトの作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-121">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="8af86-122">詳細については、「[Visual Studio で新しいプロジェクトを作成する](/visualstudio/ide/create-new-project)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-122">For more information, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

   ![スタート ウィンドウから新しいプロジェクトを作成する](razor-pages-start/_static/5/start-window-create-new-project.png)

1. <span data-ttu-id="8af86-124">**[新しいプロジェクト]** ダイアログで、 **[ASP.NET Core Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-124">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

    ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/5/np.png)
    
1. <span data-ttu-id="8af86-126">**[新しいプロジェクトの構成]** ダイアログで、 **[プロジェクト名]** に「`RazorPagesMovie`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="8af86-126">In the **Configure your new project** dialog, enter `RazorPagesMovie` for **Project name**.</span></span> <span data-ttu-id="8af86-127">サンプル コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という (大文字と小文字が一致する) 名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="8af86-127">It's important to name the project *RazorPagesMovie*, including matching the capitalization, so the namespaces will match when you copy and paste example code.</span></span>

1. <span data-ttu-id="8af86-128">**［作成］** を選択します</span><span class="sxs-lookup"><span data-stu-id="8af86-128">Select **Create**.</span></span>

    ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

1. <span data-ttu-id="8af86-130">**[新しい ASP.NET Core Web アプリケーションの作成]** ダイアログで、次のものを選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-130">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="8af86-131">ドロップダウンで **[.NET Core]** と **[ASP.NET Core 5.0]**</span><span class="sxs-lookup"><span data-stu-id="8af86-131">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="8af86-132">**Web アプリケーション**。</span><span class="sxs-lookup"><span data-stu-id="8af86-132">**Web Application**.</span></span>
    1. <span data-ttu-id="8af86-133">**Create**。</span><span class="sxs-lookup"><span data-stu-id="8af86-133">**Create**.</span></span>

     ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/5/npx.png)

    <span data-ttu-id="8af86-135">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-135">The following starter project is created:</span></span>

    ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af86-137">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af86-137">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8af86-138">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="8af86-138">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

1. <span data-ttu-id="8af86-139">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="8af86-139">Change to the directory (`cd`) which will contain the project.</span></span>

1. <span data-ttu-id="8af86-140">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-140">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * <span data-ttu-id="8af86-141">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-141">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
   * <span data-ttu-id="8af86-142">`code` コマンドを実行すると、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーが開きます。</span><span class="sxs-lookup"><span data-stu-id="8af86-142">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af86-143">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8af86-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8af86-144">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-144">Select **File** > **New Solution**.</span></span>

    ![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. <span data-ttu-id="8af86-146">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="8af86-147">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

    ![macOS Web アプリ テンプレートの選択](razor-pages-start/_static/web_app_template_vsmac.png)

1. <span data-ttu-id="8af86-149">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="8af86-149">In the **Configure the new Web Application** dialog:</span></span>

    1. <span data-ttu-id="8af86-150">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8af86-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
    1. <span data-ttu-id="8af86-151">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の .NET 5.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-151">If presented an option to select a **Target Framework**, select the latest .NET 5.x version.</span></span>
    1. <span data-ttu-id="8af86-152">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-152">Select **Next**.</span></span>

1. <span data-ttu-id="8af86-153">プロジェクトに *RazorPagesMovie* という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-153">Name the project *RazorPagesMovie* and select **Create**.</span></span>

    ![macOS でプロジェクトに名前を付ける](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="8af86-155">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="8af86-155">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="8af86-156">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="8af86-156">Examine the project files</span></span>

<span data-ttu-id="8af86-157">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8af86-157">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="8af86-158">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="8af86-158">Pages folder</span></span>

<span data-ttu-id="8af86-159">Razor ページとサポート ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8af86-159">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="8af86-160">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="8af86-160">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="8af86-161">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-161">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="8af86-162">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-162">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="8af86-163">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="8af86-163">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="8af86-164">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-164">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="8af86-165">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-165">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="8af86-166">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-166">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="8af86-167">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="8af86-167">wwwroot folder</span></span>

<span data-ttu-id="8af86-168">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的アセットが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-168">Contains static assets, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="8af86-169">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-169">For more information, see <xref:fundamentals/static-files>.</span></span>

### appsettings.json

<span data-ttu-id="8af86-170">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-170">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="8af86-171">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-171">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="8af86-172">Program.cs</span><span class="sxs-lookup"><span data-stu-id="8af86-172">Program.cs</span></span>

<span data-ttu-id="8af86-173">アプリのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-173">Contains the entry point for the app.</span></span> <span data-ttu-id="8af86-174">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-174">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="8af86-175">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="8af86-175">Startup.cs</span></span>

<span data-ttu-id="8af86-176">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8af86-176">Contains code that configures app behavior.</span></span> <span data-ttu-id="8af86-177">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-177">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8af86-178">次の手順</span><span class="sxs-lookup"><span data-stu-id="8af86-178">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8af86-179">次へ: モデルの追加</span><span class="sxs-lookup"><span data-stu-id="8af86-179">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="8af86-180">これは、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明する、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="8af86-180">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="8af86-181">コントローラーとビューを使い慣れた開発者向けの詳細情報については、「[Razor Pages の概要](xref:razor-pages/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-181">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="8af86-182">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="8af86-182">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="8af86-183">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8af86-183">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8af86-184">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="8af86-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8af86-185">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="8af86-185">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="8af86-186">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-186">Run the app.</span></span>
> * <span data-ttu-id="8af86-187">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="8af86-187">Examine the project files.</span></span>

<span data-ttu-id="8af86-188">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="8af86-188">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![[ホーム] または [Index] ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="8af86-190">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8af86-190">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af86-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af86-191">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af86-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af86-192">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af86-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8af86-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="8af86-194">Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="8af86-194">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af86-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af86-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af86-196">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-196">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="8af86-197">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-197">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="8af86-198">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="8af86-198">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="8af86-199">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="8af86-199">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="8af86-200">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="8af86-200">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="8af86-201">![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="8af86-201">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="8af86-202">ドロップダウンの **[ASP.NET Core 3.1]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-202">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="8af86-204">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-204">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af86-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af86-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8af86-207">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="8af86-207">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="8af86-208">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="8af86-208">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="8af86-209">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-209">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="8af86-210">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-210">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="8af86-211">`code` コマンドを実行すると、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーが開きます。</span><span class="sxs-lookup"><span data-stu-id="8af86-211">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="8af86-212">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="8af86-212">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="8af86-213">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-213">Select **Yes**.</span></span>

  <span data-ttu-id="8af86-214">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-214">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af86-215">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8af86-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8af86-216">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-216">Select **File** > **New Solution**.</span></span>

  ![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8af86-218">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-218">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="8af86-219">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-219">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web アプリ テンプレートの選択](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="8af86-221">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="8af86-221">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="8af86-222">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8af86-222">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="8af86-223">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 3.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-223">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="8af86-224">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-224">Select **Next**.</span></span>

* <span data-ttu-id="8af86-225">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-225">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS でプロジェクトに名前を付ける](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="8af86-227">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="8af86-227">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="8af86-228">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="8af86-228">Examine the project files</span></span>

<span data-ttu-id="8af86-229">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8af86-229">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="8af86-230">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="8af86-230">Pages folder</span></span>

<span data-ttu-id="8af86-231">Razor ページとサポート ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8af86-231">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="8af86-232">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="8af86-232">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="8af86-233">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-233">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="8af86-234">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-234">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="8af86-235">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="8af86-235">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="8af86-236">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-236">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="8af86-237">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-237">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="8af86-238">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-238">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="8af86-239">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="8af86-239">wwwroot folder</span></span>

<span data-ttu-id="8af86-240">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-240">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="8af86-241">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-241">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="8af86-242">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="8af86-242">appSettings.json</span></span>

<span data-ttu-id="8af86-243">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-243">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="8af86-244">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-244">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="8af86-245">Program.cs</span><span class="sxs-lookup"><span data-stu-id="8af86-245">Program.cs</span></span>

<span data-ttu-id="8af86-246">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-246">Contains the entry point for the program.</span></span> <span data-ttu-id="8af86-247">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-247">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="8af86-248">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="8af86-248">Startup.cs</span></span>

<span data-ttu-id="8af86-249">アプリの動作を構成するコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8af86-249">Contains code that configures app behavior.</span></span> <span data-ttu-id="8af86-250">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-250">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8af86-251">次の手順</span><span class="sxs-lookup"><span data-stu-id="8af86-251">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8af86-252">次へ: モデルの追加</span><span class="sxs-lookup"><span data-stu-id="8af86-252">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8af86-253">これは、シリーズの最初のチュートリアルです。</span><span class="sxs-lookup"><span data-stu-id="8af86-253">This is the first tutorial of a series.</span></span> <span data-ttu-id="8af86-254">[このシリーズ](xref:tutorials/razor-pages/index)では、ASP.NET Core の Razor Pages Web アプリの構築の基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="8af86-254">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

<span data-ttu-id="8af86-255">コントローラーとビューを使い慣れた開発者向けの詳細情報については、「[Razor Pages の概要](xref:razor-pages/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-255">For a more advanced introduction aimed at developers who are familiar with controllers and views, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="8af86-256">シリーズの最後には、映画のデータベースを管理できるアプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="8af86-256">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

<span data-ttu-id="8af86-257">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="8af86-257">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="8af86-258">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="8af86-258">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8af86-259">Razor Pages Web アプリを作成する。</span><span class="sxs-lookup"><span data-stu-id="8af86-259">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="8af86-260">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-260">Run the app.</span></span>
> * <span data-ttu-id="8af86-261">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="8af86-261">Examine the project files.</span></span>

<span data-ttu-id="8af86-262">このチュートリアルの最後には、以降のチュートリアルでビルドする作業用 Razor Pages Web アプリができあがります。</span><span class="sxs-lookup"><span data-stu-id="8af86-262">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![[ホーム] または [Index] ページ](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="8af86-264">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="8af86-264">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af86-265">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af86-265">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af86-266">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af86-266">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af86-267">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8af86-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="8af86-268">Razor Pages Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="8af86-268">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af86-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af86-269">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af86-270">Visual Studio の **[ファイル]** メニューから、 **[新規作成]** > **[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-270">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="8af86-271">新しい ASP.NET CoreWeb アプリケーションを作成し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-271">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="8af86-273">プロジェクトに **RazorPagesMovie** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="8af86-273">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="8af86-274">コードのコピーおよび貼り付けを行う際に名前空間が一致するように、プロジェクトに *RazorPagesMovie* という名前を付けることが重要です。</span><span class="sxs-lookup"><span data-stu-id="8af86-274">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/config.png)

* <span data-ttu-id="8af86-276">ドロップダウンの **[ASP.NET Core 2.2]** 、 **[Web アプリケーション]** の順に選択し、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-276">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新しい ASP.NET Core Web アプリケーション](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="8af86-278">次のスターター プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-278">The following starter project is created:</span></span>

  ![ソリューション エクスプローラー](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="8af86-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af86-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8af86-281">[統合ターミナル](https://code.visualstudio.com/docs/editor/integrated-terminal)を開きます。</span><span class="sxs-lookup"><span data-stu-id="8af86-281">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="8af86-282">プロジェクトを格納するディレクトリ (`cd`) に変更します。</span><span class="sxs-lookup"><span data-stu-id="8af86-282">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="8af86-283">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-283">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="8af86-284">`dotnet new` コマンド: *RazorPagesMovie* フォルダーに新しい Razor Pages プロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-284">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="8af86-285">`code` コマンドを実行すると、Visual Studio Code の現在のインスタンス内で *RazorPagesMovie* フォルダーが開きます。</span><span class="sxs-lookup"><span data-stu-id="8af86-285">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="8af86-286">状態バーの OmniSharp フレーム アイコンが緑色になり、"**ビルドとデバッグに必要な資産が 'RazorPagesMovie' にありません。追加しますか?** " という内容のダイアログ ボックスが表示されたら、</span><span class="sxs-lookup"><span data-stu-id="8af86-286">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="8af86-287">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-287">Select **Yes**.</span></span>

  <span data-ttu-id="8af86-288">*launch.json* ファイルと *tasks.json* ファイルを格納している *.vscode* ディレクトリが、プロジェクトのルート ディレクトリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-288">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af86-289">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8af86-289">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8af86-290">**[ファイル]** > **[新しいソリューション]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-290">Select **File** > **New Solution**.</span></span>

![macOS の新しいソリューション](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="8af86-292">バージョン 8.6 より前の Visual Studio for Mac では、 **[.NET Core]** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-292">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="8af86-293">バージョン 8.6 以降では、 **[Web and Console]\(Web とコンソール\)** 、 **[アプリ]** 、 **[Web アプリケーション]** 、 **[次へ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-293">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="8af86-294">**[Configure your new Web Application]\(新しい Web アプリケーションを構成する\)** ダイアログで、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="8af86-294">In the **Configure the new Web Application** dialog:</span></span>

  * <span data-ttu-id="8af86-295">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8af86-295">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="8af86-296">**[ターゲット フレームワーク]** を選択するオプションが表示された場合は、最新の 2.x バージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-296">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="8af86-297">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-297">Select **Next**.</span></span>

* <span data-ttu-id="8af86-298">プロジェクトに **RazorPagesMovie** という名前を付けて、 **[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="8af86-298">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="8af86-300">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="8af86-300">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8af86-301">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8af86-301">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8af86-302">Ctrl + F5 キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-302">Press Ctrl+F5 to run without the debugger.</span></span>

  <span data-ttu-id="8af86-303"><kbd>Ctrl + F5</kbd> キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、およびコード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8af86-303">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8af86-304">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="8af86-304">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="8af86-305">Visual Studio で [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) が開始され、アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-305">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="8af86-306">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-306">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8af86-307">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="8af86-307">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="8af86-308">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-308">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="8af86-309">Visual Studio が Web プロジェクトを作成する場合は、Web サーバーにランダム ポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-309">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="8af86-310">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="8af86-310">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="8af86-311">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="8af86-311">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![[ホーム] または [Index] ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="8af86-313">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="8af86-313">The following image shows the app after consent to tracking is provided:</span></span>

  ![[ホーム] または [Index] ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="8af86-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8af86-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="8af86-316"><kbd>Ctrl + F5</kbd> キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-316">Press <kbd>Ctrl+F5</kbd> to run without the debugger.</span></span>

  <span data-ttu-id="8af86-317"><kbd>Ctrl + F5</kbd> キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、およびコード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8af86-317">Launching the app with <kbd>Ctrl+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8af86-318">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="8af86-318">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="8af86-319">Visual Studio Code で [Kestrel](xref:fundamentals/servers/kestrel) が開始され、ブラウザーが起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="8af86-319">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span> <span data-ttu-id="8af86-320">アドレス バーには、`example.com` などではなく、`localhost:port#` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-320">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="8af86-321">これは、`localhost` がローカル コンピューターの標準のホスト名であるためです。</span><span class="sxs-lookup"><span data-stu-id="8af86-321">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="8af86-322">localhost では、ローカル コンピューターからの Web 要求のみが処理されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-322">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="8af86-323">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="8af86-323">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="8af86-324">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="8af86-324">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![[ホーム] または [Index] ページ](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="8af86-326">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="8af86-326">The following image shows the app after you give consent to tracking:</span></span>

  ![[ホーム] または [Index] ページ](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8af86-328">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8af86-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="8af86-329">**Cmd - Opt - F5** キーを押して、デバッガーなしで実行します。</span><span class="sxs-lookup"><span data-stu-id="8af86-329">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="8af86-330"><kbd>Cmd + Opt + F5</kbd> キー (非デバッグ モード) でアプリを起動することで、コードの変更、ファイルの保存、ブラウザーの更新、コード変更の確認を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8af86-330">Launching the app with <kbd>Cmd+Opt+F5</kbd> (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="8af86-331">多くの開発者は、すばやくアプリを起動し、変更を確認できる非デバッグ モードの使用を好みます。</span><span class="sxs-lookup"><span data-stu-id="8af86-331">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>

  <span data-ttu-id="8af86-332">Visual Studio は [Kestrel](xref:fundamentals/servers/kestrel) を開始し、ブラウザーを起動して、`http://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="8af86-332">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and goes to `http://localhost:5001`.</span></span>

* <span data-ttu-id="8af86-333">アプリのホーム ページ上で、 **[同意する]** を選択して、追跡に同意します。</span><span class="sxs-lookup"><span data-stu-id="8af86-333">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="8af86-334">このアプリによって個人情報は追跡されません。しかし、欧州連合の[一般データ保護規則 (GDPR)](xref:security/gdpr) に準拠するための同意機能が必要な場合は、プロジェクト テンプレートにその機能が含められます。</span><span class="sxs-lookup"><span data-stu-id="8af86-334">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![[ホーム] または [Index] ページ](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="8af86-336">次の図では、追跡に同意した後のアプリを示しています。</span><span class="sxs-lookup"><span data-stu-id="8af86-336">The following image shows the app after consent to tracking is provided:</span></span>

  ![[ホーム] または [Index] ページ](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="8af86-338">プロジェクト ファイルを確認する</span><span class="sxs-lookup"><span data-stu-id="8af86-338">Examine the project files</span></span>

<span data-ttu-id="8af86-339">以降のチュートリアルで使用するメイン プロジェクト フォルダーとファイルの概要を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8af86-339">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="8af86-340">Pages フォルダー</span><span class="sxs-lookup"><span data-stu-id="8af86-340">Pages folder</span></span>

<span data-ttu-id="8af86-341">Razor ページとサポート ファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8af86-341">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="8af86-342">各 Razor ページは、次のファイルのペアとなります。</span><span class="sxs-lookup"><span data-stu-id="8af86-342">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="8af86-343">*.cshtml* ファイル: HTML マークアップと、Razor 構文を使用した C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-343">A *.cshtml* file that has HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="8af86-344">*.cshtml.cs* ファイル: ページ イベントを処理する C# コードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-344">A *.cshtml.cs* file that has C# code that handles page events.</span></span>

<span data-ttu-id="8af86-345">サポート ファイルには、アンダー スコアで始まる名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="8af86-345">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="8af86-346">たとえば、 *_Layout.cshtml* ファイルでは、すべてのページに共通の UI 要素が構成されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-346">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="8af86-347">このファイルでは、ページの上部に表示されるナビゲーション メニューと、ページの下部に表示される著作権の通知が設定されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-347">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="8af86-348">詳細については、「<xref:mvc/views/layout>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-348">For more information, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="8af86-349">Razor ページは `PageModel` から派生します。</span><span class="sxs-lookup"><span data-stu-id="8af86-349">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="8af86-350">慣例により、`PageModel` 派生クラスは `<PageName>Model` と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8af86-350">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="8af86-351">wwwroot フォルダー</span><span class="sxs-lookup"><span data-stu-id="8af86-351">wwwroot folder</span></span>

<span data-ttu-id="8af86-352">HTML ファイル、JavaScript ファイル、CSS ファイルなどの静的ファイルが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-352">Contains static files, like HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="8af86-353">詳細については、「<xref:fundamentals/static-files>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-353">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="8af86-354">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="8af86-354">appSettings.json</span></span>

<span data-ttu-id="8af86-355">接続文字列などの構成データが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-355">Contains configuration data, like connection strings.</span></span> <span data-ttu-id="8af86-356">詳細については、「<xref:fundamentals/configuration/index>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-356">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="8af86-357">Program.cs</span><span class="sxs-lookup"><span data-stu-id="8af86-357">Program.cs</span></span>

<span data-ttu-id="8af86-358">プログラムのエントリ ポイントが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-358">Contains the entry point for the program.</span></span> <span data-ttu-id="8af86-359">詳細については、「<xref:fundamentals/host/generic-host>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-359">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="8af86-360">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="8af86-360">Startup.cs</span></span>

<span data-ttu-id="8af86-361">cookie に対する同意が必要かどうかなど、アプリの動作を構成するコードが保存されます。</span><span class="sxs-lookup"><span data-stu-id="8af86-361">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="8af86-362">詳細については、「<xref:fundamentals/startup>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8af86-362">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8af86-363">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="8af86-363">Additional resources</span></span>

* [<span data-ttu-id="8af86-364">このチュートリアルの YouTube バージョン</span><span class="sxs-lookup"><span data-stu-id="8af86-364">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="8af86-365">次のステップ</span><span class="sxs-lookup"><span data-stu-id="8af86-365">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8af86-366">次へ: モデルの追加</span><span class="sxs-lookup"><span data-stu-id="8af86-366">Next: Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
