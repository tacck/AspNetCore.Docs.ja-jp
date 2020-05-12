---
title: Blazor WebAssembly で ASP.NET Core SignalR を使用する
author: guardrex
description: Blazor WebAssembly で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773789"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="99a03-103">Blazor WebAssembly で ASP.NET Core SignalR を使用する</span><span class="sxs-lookup"><span data-stu-id="99a03-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="99a03-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="99a03-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="99a03-105">このチュートリアルでは、Blazor WebAssembly で SignalR を使用してリアルタイム アプリを構築するための基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="99a03-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="99a03-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="99a03-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="99a03-107">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="99a03-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="99a03-108">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="99a03-109">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="99a03-110">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="99a03-111">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-111">Add Razor component code for chat</span></span>

<span data-ttu-id="99a03-112">このチュートリアルの最後には、動作するチャット アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="99a03-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="99a03-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="99a03-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="99a03-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="99a03-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99a03-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99a03-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="99a03-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99a03-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99a03-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99a03-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="99a03-118">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99a03-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="99a03-119">ホストされる Blazor WebAssembly アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="99a03-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="99a03-120">Visual Studio バージョン 16.6 Preview 2 以降を使用していない場合は、[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="99a03-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="99a03-121">Blazor WebAssembly がプレビュー段階にある間、[Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) パッケージにはプレビュー バージョンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="99a03-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="99a03-122">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

<span data-ttu-id="99a03-123">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="99a03-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99a03-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99a03-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="99a03-125">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="99a03-125">Create a new project.</span></span>

1. <span data-ttu-id="99a03-126">**[Blazor アプリ]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="99a03-127">**[プロジェクト名]** フィールドに「BlazorSignalRApp」と入力します。</span><span class="sxs-lookup"><span data-stu-id="99a03-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="99a03-128">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="99a03-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="99a03-129">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-129">Select **Create**.</span></span>

1. <span data-ttu-id="99a03-130">**[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="99a03-131">**[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="99a03-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="99a03-132">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="99a03-133">新しいバージョンの Visual Studio にアップグレードした場合、またはインストールした場合で、VS の UI に Blazor WebAssembly テンプレートが表示されないときは、前述の `dotnet new` コマンドを使用してテンプレートを再インストールしてください。</span><span class="sxs-lookup"><span data-stu-id="99a03-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99a03-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99a03-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="99a03-135">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="99a03-136">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="99a03-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="99a03-137">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="99a03-138">Visual Studio Code によって、生成された *launch.json* および *tasks.json* ファイルを含む *.vscode* フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="99a03-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99a03-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99a03-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="99a03-140">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="99a03-141">Visual Studio for Mac で、プロジェクト フォルダーに移動してプロジェクトのソリューション ファイル ( *.sln*) を開くことで、プロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="99a03-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="99a03-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99a03-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="99a03-143">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="99a03-144">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99a03-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99a03-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="99a03-146">**ソリューション エクスプローラー**で、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="99a03-147">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が *nuget.org* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="99a03-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="99a03-148">**[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。</span><span class="sxs-lookup"><span data-stu-id="99a03-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="99a03-149">検索結果から `Microsoft.AspNetCore.SignalR.Client` パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="99a03-150">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="99a03-151">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99a03-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99a03-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="99a03-153">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99a03-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99a03-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="99a03-155">**[ソリューション]** サイド バーで、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="99a03-156">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが *nuget.org* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="99a03-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="99a03-157">**[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。</span><span class="sxs-lookup"><span data-stu-id="99a03-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="99a03-158">検索結果の `Microsoft.AspNetCore.SignalR.Client` パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="99a03-159">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="99a03-160">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99a03-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="99a03-161">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="99a03-162">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-162">Add a SignalR hub</span></span>

<span data-ttu-id="99a03-163">**BlazorSignalRApp.Server** プロジェクトで、*Hubs* (複数形) フォルダーを作成し、次の `ChatHub` クラスを追加します (*Hubs/ChatHub.cs*)。</span><span class="sxs-lookup"><span data-stu-id="99a03-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="99a03-164">SignalR ハブのサービスとエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="99a03-165">**BlazorSignalRApp.Server** プロジェクトで、*Startup.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="99a03-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="99a03-166">ファイルの先頭に `ChatHub` クラスの名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="99a03-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="99a03-167">SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="99a03-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="99a03-168">`Startup.Configure` で、コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="99a03-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="99a03-169">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="99a03-170">**BlazorSignalRApp.Client** プロジェクトで、*Pages/Index.razor* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="99a03-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="99a03-171">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="99a03-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="99a03-172">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="99a03-172">Run the app</span></span>

1. <span data-ttu-id="99a03-173">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="99a03-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="99a03-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="99a03-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="99a03-175">**ソリューション エクスプローラー**で、**BlazorSignalRApp.Server** プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="99a03-176"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="99a03-177">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="99a03-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="99a03-178">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="99a03-179">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="99a03-179">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="99a03-181">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="99a03-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="99a03-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="99a03-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="99a03-183">VS Code により、サーバー アプリの起動プロファイルの作成が提案される場合 ( *.vscode/launch.json*)、`program` エントリが次のように表示され、アプリのアセンブリをポイントします (`{APPLICATION NAME}.Server.dll`)。</span><span class="sxs-lookup"><span data-stu-id="99a03-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="99a03-184"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="99a03-185">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="99a03-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="99a03-186">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="99a03-187">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="99a03-187">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="99a03-189">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="99a03-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="99a03-190">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="99a03-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="99a03-191">**[ソリューション]** サイド バーで、**BlazorSignalRApp.Server** プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="99a03-192"><kbd>⌘</kbd>+<kbd>↩</kbd>\* \* を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="99a03-193">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="99a03-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="99a03-194">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="99a03-195">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="99a03-195">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="99a03-197">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="99a03-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="99a03-198">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="99a03-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="99a03-199">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="99a03-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="99a03-200">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="99a03-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="99a03-201">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="99a03-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="99a03-202">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="99a03-202">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="99a03-204">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="99a03-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="99a03-205">次の手順</span><span class="sxs-lookup"><span data-stu-id="99a03-205">Next steps</span></span>

<span data-ttu-id="99a03-206">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="99a03-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="99a03-207">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="99a03-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="99a03-208">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="99a03-209">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="99a03-210">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="99a03-211">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="99a03-211">Add Razor component code for chat</span></span>

<span data-ttu-id="99a03-212">Blazor アプリの構築について詳しくは、Blazor のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="99a03-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="99a03-213">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="99a03-213">Additional resources</span></span>

* <xref:signalr/introduction>
