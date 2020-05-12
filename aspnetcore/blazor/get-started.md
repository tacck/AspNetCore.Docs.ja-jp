---
title: ASP.NET Core Blazor の概要
author: guardrex
description: 希望のツールで Blazor アプリを構築することで、Blazor の利用を開始してください。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769456"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="a4f57-103">ASP.NET Core Blazor の概要</span><span class="sxs-lookup"><span data-stu-id="a4f57-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="a4f57-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a4f57-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a4f57-105">Blazor の使用を開始するには、使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="a4f57-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4f57-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4f57-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a4f57-107">Blazor サーバー アプリを作成するには、**ASP.NET および Web 開発**ワークロードと共に、最新バージョンの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4f57-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="a4f57-108">Blazor サーバー アプリと Blazor WebAssembly アプリを作成するには、**ASP.NET および Web 開発**ワークロードと共に、[Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) の最新プレビューをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4f57-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="a4f57-109">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4f57-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a4f57-110">次のコマンドを実行して、Blazor WebAssembly プレビュー テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4f57-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="a4f57-111">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-111">Create a new project.</span></span>

1. <span data-ttu-id="a4f57-112">**[Blazor アプリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-112">Select **Blazor App**.</span></span> <span data-ttu-id="a4f57-113">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-113">Select **Next**.</span></span>

1. <span data-ttu-id="a4f57-114">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a4f57-115">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a4f57-116">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-116">Select **Create**.</span></span>

1. <span data-ttu-id="a4f57-117">Blazor WebAssembly エクスペリエンス (Visual Studio 16.6 Preview 2 以降) については、 **[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a4f57-118">Blazor サーバー エクスペリエンス (Visual Studio 16.4 以降) については、 **[Blazor サーバー アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a4f57-119">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-119">Select **Create**.</span></span>

1. <span data-ttu-id="a4f57-120"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4f57-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4f57-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="a4f57-122">[.NET Core 3.1 SDK をインストールします](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="a4f57-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="a4f57-123">必要に応じて、次のコマンドを実行して、[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) プレビュー テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4f57-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="a4f57-124">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4f57-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a4f57-125">3\.2 Preview Blazor WebAssembly テンプレートを使用するには、[.NET Core SDK バージョン 3.1.201 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)が**必要です**。</span><span class="sxs-lookup"><span data-stu-id="a4f57-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="a4f57-126">コマンド シェルで `dotnet --version` を実行して、インストールされている .NET Core SDK バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="a4f57-127">[Visual Studio Code](https://code.visualstudio.com/) のインストール。</span><span class="sxs-lookup"><span data-stu-id="a4f57-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="a4f57-128">`debug.javascript.usePreview` が `true` に設定された状態で、最新の [Visual Studio Code 拡張機能用の C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) と [JavaScript デバッガー (夜間)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) 拡張機能をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4f57-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="a4f57-129">Blazor Server エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="a4f57-130">Blazor WebAssembly エクスペリエンスについては、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="a4f57-131">Visual Studio Code で *WebApplication1* フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="a4f57-132">IDE によって、プロジェクトをビルドおよびデバッグするためにアセットを追加するよう要求されます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a4f57-133">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-133">Select **Yes**.</span></span>

1. <span data-ttu-id="a4f57-134">Blazor サーバーを使用し、Visual Studio Code デバッガーを使用してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="a4f57-135">Blazor WebAssembly を使用し、 **.NET Core Launch (Blazor Standalone)** の起動構成を使用してアプリを起動します。次に、**Chrome の .NET Core Debug Blazor Web Assembly** の起動構成 (Chrome が必要) を使用してブラウザーを起動します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="a4f57-136">詳細については、「<xref:blazor/debug#visual-studio-code>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4f57-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="a4f57-137">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4f57-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4f57-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a4f57-139">Visual Studio for Mac では Blazor サーバーがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a4f57-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="a4f57-140">現時点では、Blazor WebAssembly はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a4f57-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="a4f57-141">macOS で Blazor WebAssembly アプリを作成するには、 **[.NET Core CLI]** タブのガイダンスに従ってください。2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4f57-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a4f57-142">[Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="a4f57-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="a4f57-143">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から**新しい**プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="a4f57-144">サイドバーで、 **[.NET Core]**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="a4f57-145">**[Blazor Server App] (Blazor Server アプリ)** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="a4f57-146">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-146">Select **Next**.</span></span>

1. <span data-ttu-id="a4f57-147">次の構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="a4f57-148">**[ターゲット フレームワーク]** が **[.NET Core 3.1]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="a4f57-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="a4f57-149">**[認証]** が **[認証なし]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="a4f57-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="a4f57-150">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-150">Select **Next**.</span></span>

1. <span data-ttu-id="a4f57-151">**[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="a4f57-152">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-152">Select **Create**.</span></span>

1. <span data-ttu-id="a4f57-153">*デバッガーを使用せずに*アプリを実行するには、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="a4f57-154">デバッグは、現時点ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="a4f57-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="a4f57-155">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="a4f57-156">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="a4f57-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a4f57-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a4f57-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="a4f57-158">[.NET Core 3.1 SDK をインストールします](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="a4f57-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="a4f57-159">必要に応じて、次のコマンドを実行して、Blazor WebAssembly プレビュー テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a4f57-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="a4f57-160">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor サーバー*については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a4f57-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="a4f57-161">3\.2 Preview Blazor WebAssembly テンプレートを使用するには、[.NET Core SDK バージョン 3.1.201 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)が**必要です**。</span><span class="sxs-lookup"><span data-stu-id="a4f57-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="a4f57-162">コマンド シェルで `dotnet --version` を実行して、インストールされている .NET Core SDK バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="a4f57-163">Blazor Server エクスペリエンスについては、コマンド シェルで以下のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="a4f57-164">Blazor WebAssembly エクスペリエンスについては、コマンド シェルで以下のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="a4f57-165">ブラウザーで、`https://localhost:5001` に移動します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="a4f57-166">サイドバーのタブから複数のページを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="a4f57-167">Home</span><span class="sxs-lookup"><span data-stu-id="a4f57-167">Home</span></span>
* <span data-ttu-id="a4f57-168">カウンター</span><span class="sxs-lookup"><span data-stu-id="a4f57-168">Counter</span></span>
* <span data-ttu-id="a4f57-169">Fetch data (データのフェッチ)</span><span class="sxs-lookup"><span data-stu-id="a4f57-169">Fetch data</span></span>

<span data-ttu-id="a4f57-170">Counter ページ上で **[クリックしてください]** ボタンを選択し、ページを更新することなくカウンターをインクリメントします。</span><span class="sxs-lookup"><span data-stu-id="a4f57-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="a4f57-171">Web ページでカウンターをインクリメントするには、通常、JavaScript を記述することが必要ですが、Blazor では C# を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="a4f57-172">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a4f57-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="a4f57-173">ブラウザーで `/counter` の要求があると、上部の `@page` ディレクティブで指定されているように、`Counter` コンポーネントによってそのコンテンツがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="a4f57-174">コンポーネントは、レンダリング ツリーのメモリ内表現としてレンダリングされ、柔軟かつ効率的な方法で UI を更新するために利用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="a4f57-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="a4f57-175">**[クリックしてください]** ボタンを選択するたびに:</span><span class="sxs-lookup"><span data-stu-id="a4f57-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="a4f57-176">`onclick` イベントが発生します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="a4f57-177">`IncrementCount` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="a4f57-178">`currentCount` がインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="a4f57-179">コンポーネントが再度レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-179">The component is rendered again.</span></span>

<span data-ttu-id="a4f57-180">ランタイムで新しいコンテンツと前のコンテンツが比較され、変更されたコンテンツのみがドキュメント オブジェクト モデル (DOM) に適用されます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="a4f57-181">HTML 構文を使用して、別のコンポーネントにコンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="a4f57-182">たとえば、`Index` コンポーネントに `<Counter />` 要素を追加することで、アプリのホームページに `Counter` コンポーネントを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="a4f57-183">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a4f57-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="a4f57-184">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-184">Run the app.</span></span> <span data-ttu-id="a4f57-185">ホームページには、`Counter` コンポーネントによって提供される独自のカウンターがあります。</span><span class="sxs-lookup"><span data-stu-id="a4f57-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="a4f57-186">コンポーネント パラメーターは、属性または [子コンテンツ](xref:blazor/components#child-content)を使用して指定されます。これにより、子コンポーネントのプロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="a4f57-187">`Counter` コンポーネントにパラメーターを追加するには、コンポーネントの `@code` ブロックを更新します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="a4f57-188">属性 `[Parameter]` を使用して、`IncrementAmount` のためのパブリック プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="a4f57-189">`currentCount` の値を増やすときに `IncrementAmount` を使うように `IncrementCount` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="a4f57-190">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="a4f57-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="a4f57-191">属性を使用して、`Index` コンポーネントの `<Counter>` 要素内に `IncrementAmount` を指定します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="a4f57-192">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="a4f57-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="a4f57-193">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a4f57-193">Run the app.</span></span> <span data-ttu-id="a4f57-194">`Index` コンポーネントは、 **[クリックしてください]** ボタンが選択されるたびに 10 ずつインクリメントされる独自のカウンターを持っています。</span><span class="sxs-lookup"><span data-stu-id="a4f57-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="a4f57-195">`/counter` にある `Counter` コンポーネント (*Counter.razor*) は、引き続き 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="a4f57-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a4f57-196">次の手順</span><span class="sxs-lookup"><span data-stu-id="a4f57-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="a4f57-197">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a4f57-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
