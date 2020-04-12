---
title: Blazor WebAssembly で ASP.NET Core SignalR を使用する
author: guardrex
description: Blazor WebAssembly で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: c4843dc282e1978b39738e206ecc79ded87fcff9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306565"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="0530b-103">Blazor WebAssembly で ASP.NET Core SignalR を使用する</span><span class="sxs-lookup"><span data-stu-id="0530b-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="0530b-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0530b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0530b-105">このチュートリアルでは、Blazor WebAssembly で SignalR を使用してリアルタイム アプリを構築するための基本について説明します。</span><span class="sxs-lookup"><span data-stu-id="0530b-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="0530b-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0530b-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0530b-107">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="0530b-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0530b-108">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="0530b-109">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="0530b-110">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0530b-111">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-111">Add Razor component code for chat</span></span>

<span data-ttu-id="0530b-112">このチュートリアルの最後には、動作するチャット アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="0530b-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="0530b-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0530b-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0530b-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="0530b-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0530b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0530b-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="0530b-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0530b-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0530b-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0530b-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="0530b-118">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0530b-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="0530b-119">ホストされる Blazor WebAssembly アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="0530b-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="0530b-120">Visual Studio バージョン 16.6 Preview 2 以降を使用していない場合は、[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) テンプレートをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0530b-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="0530b-121">Blazor WebAssembly がプレビュー段階にある間、[Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) パッケージにはプレビュー バージョンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="0530b-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="0530b-122">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0530b-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
```

<span data-ttu-id="0530b-123">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="0530b-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0530b-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0530b-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0530b-125">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="0530b-125">Create a new project.</span></span>

1. <span data-ttu-id="0530b-126">**[Blazor アプリ]** を選択し、 **[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="0530b-127">**[プロジェクト名]** フィールドに「BlazorSignalRApp」と入力します。</span><span class="sxs-lookup"><span data-stu-id="0530b-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="0530b-128">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="0530b-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0530b-129">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-129">Select **Create**.</span></span>

1. <span data-ttu-id="0530b-130">**[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="0530b-131">**[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="0530b-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="0530b-132">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="0530b-133">新しいバージョンの Visual Studio にアップグレードした場合、またはインストールした場合で、VS の UI に Blazor WebAssembly テンプレートが表示されないときは、前述の `dotnet new` コマンドを使用してテンプレートを再インストールしてください。</span><span class="sxs-lookup"><span data-stu-id="0530b-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0530b-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0530b-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0530b-135">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0530b-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="0530b-136">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="0530b-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="0530b-137">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="0530b-138">Visual Studio Code によって、生成された *launch.json* および *tasks.json* ファイルを含む *.vscode* フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0530b-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0530b-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0530b-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0530b-140">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0530b-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="0530b-141">Visual Studio for Mac で、プロジェクト フォルダーに移動してプロジェクトのソリューション ファイル ( *.sln*) を開くことで、プロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="0530b-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0530b-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0530b-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0530b-143">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0530b-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="0530b-144">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0530b-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0530b-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="0530b-146">**ソリューション エクスプローラー**で、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0530b-147">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が *nuget.org* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0530b-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="0530b-148">**[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。</span><span class="sxs-lookup"><span data-stu-id="0530b-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="0530b-149">検索結果から `Microsoft.AspNetCore.SignalR.Client` パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="0530b-150">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="0530b-151">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0530b-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0530b-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="0530b-153">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0530b-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0530b-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0530b-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0530b-155">**[ソリューション]** サイド バーで、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="0530b-156">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが *nuget.org* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="0530b-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="0530b-157">**[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。</span><span class="sxs-lookup"><span data-stu-id="0530b-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="0530b-158">検索結果の `Microsoft.AspNetCore.SignalR.Client` パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="0530b-159">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0530b-160">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0530b-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="0530b-161">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0530b-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="0530b-162">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-162">Add a SignalR hub</span></span>

<span data-ttu-id="0530b-163">**BlazorSignalRApp.Server** プロジェクトで、*Hubs* (複数形) フォルダーを作成し、次の `ChatHub` クラスを追加します (*Hubs/ChatHub.cs*)。</span><span class="sxs-lookup"><span data-stu-id="0530b-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="0530b-164">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="0530b-165">**BlazorSignalRApp.Server** プロジェクトで、*Startup.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="0530b-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="0530b-166">ファイルの先頭に `ChatHub` クラスの名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="0530b-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="0530b-167">`Startup.ConfigureServices` に SignalR サービスを追加します。</span><span class="sxs-lookup"><span data-stu-id="0530b-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="0530b-168">`Startup.Configure` で、既定のコントローラー ルートのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="0530b-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="0530b-169">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="0530b-170">**BlazorSignalRApp.Client** プロジェクトで、*Pages/Index.razor* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="0530b-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="0530b-171">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0530b-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="0530b-172">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="0530b-172">Run the app</span></span>

1. <span data-ttu-id="0530b-173">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="0530b-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0530b-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0530b-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0530b-175">**ソリューション エクスプローラー**で、**BlazorSignalRApp.Server** プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="0530b-176">**Ctrl + F5** キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="0530b-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="0530b-177">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0530b-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0530b-178">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0530b-179">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="0530b-179">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0530b-181">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0530b-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0530b-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0530b-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="0530b-183">ツール バーで **[デバッグ]**  >  **[デバッグなしで実行]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="0530b-184">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0530b-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0530b-185">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0530b-186">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="0530b-186">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0530b-188">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0530b-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0530b-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0530b-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0530b-190">**[ソリューション]** サイド バーで、**BlazorSignalRApp.Server** プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="0530b-191">メニューから、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="0530b-192">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0530b-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0530b-193">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0530b-194">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="0530b-194">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0530b-196">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0530b-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0530b-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0530b-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="0530b-198">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="0530b-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="0530b-199">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="0530b-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="0530b-200">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0530b-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="0530b-201">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="0530b-201">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="0530b-203">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="0530b-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="0530b-204">次の手順</span><span class="sxs-lookup"><span data-stu-id="0530b-204">Next steps</span></span>

<span data-ttu-id="0530b-205">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="0530b-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0530b-206">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="0530b-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="0530b-207">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="0530b-208">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="0530b-209">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="0530b-210">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="0530b-210">Add Razor component code for chat</span></span>

<span data-ttu-id="0530b-211">Blazor アプリの構築について詳しくは、Blazor のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0530b-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="0530b-212">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0530b-212">Additional resources</span></span>

* <xref:signalr/introduction>
