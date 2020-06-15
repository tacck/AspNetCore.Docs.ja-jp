---
title: Blazor WebAssembly で ASP.NET Core SignalR を使用する
author: guardrex
description: Blazor WebAssembly で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 720f534426cc0e2b32778e49050c7f7d75ecd60d
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679593"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="4c293-103">Blazor WebAssembly で ASP.NET Core SignalR を使用する</span><span class="sxs-lookup"><span data-stu-id="4c293-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="4c293-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4c293-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4c293-105">このチュートリアルでは、Blazor WebAssembly と共に SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="4c293-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="4c293-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="4c293-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4c293-107">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="4c293-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="4c293-108">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="4c293-109">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="4c293-110">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="4c293-111">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-111">Add Razor component code for chat</span></span>

<span data-ttu-id="4c293-112">このチュートリアルの最後には、動作するチャット アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="4c293-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="4c293-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="4c293-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4c293-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4c293-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4c293-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c293-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4c293-116">**ASP.NET および Web 開発**ワークロードを含む [Visual Studio 2019 16.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="4c293-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4c293-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4c293-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4c293-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4c293-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="4c293-119">Visual Studio for Mac バージョン 8.6 以降</span><span class="sxs-lookup"><span data-stu-id="4c293-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="4c293-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c293-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="4c293-121">ホストされた Blazor WebAssembly アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="4c293-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="4c293-122">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4c293-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4c293-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c293-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="4c293-124">Visual Studio 16.6 以降と .NET Core SDK 3.1.300 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="4c293-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="4c293-125">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4c293-125">Create a new project.</span></span>

1. <span data-ttu-id="4c293-126">**[Blazor アプリ]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="4c293-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="4c293-127">**[プロジェクト名]** フィールドに「BlazorSignalRApp」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4c293-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="4c293-128">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="4c293-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="4c293-129">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-129">Select **Create**.</span></span>

1. <span data-ttu-id="4c293-130">**[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="4c293-131">**[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4c293-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="4c293-132">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="4c293-133">新しいバージョンの Visual Studio にアップグレードした場合、またはインストールした場合で、VS の UI に Blazor WebAssembly テンプレートが表示されないときは、前述の `dotnet new` コマンドを使用してテンプレートを再インストールしてください。</span><span class="sxs-lookup"><span data-stu-id="4c293-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4c293-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4c293-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4c293-135">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="4c293-136">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="4c293-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="4c293-137">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="4c293-138">Visual Studio Code によって、生成された *launch.json* および *tasks.json* ファイルを含む *.vscode* フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="4c293-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4c293-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4c293-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4c293-140">最新バージョンの [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) をインストールし、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="4c293-141">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から**新しい**プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="4c293-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="4c293-142">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="4c293-143">**[Blazor WebAssembly アプリ]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="4c293-144">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-144">Select **Next**.</span></span>

   <span data-ttu-id="4c293-145">次の構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="4c293-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="4c293-146">**[ターゲット フレームワーク]** が **[.NET Core 3.1]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="4c293-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="4c293-147">**[認証]** が **[認証なし]** に設定されている</span><span class="sxs-lookup"><span data-stu-id="4c293-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="4c293-148">**[ASP.NET Core Hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="4c293-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="4c293-149">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-149">Select **Next**.</span></span>

1. <span data-ttu-id="4c293-150">**[プロジェクト名]** フィールドで、アプリに `BlazorSignalRApp` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="4c293-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="4c293-151">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-151">Select **Create**.</span></span>

   <span data-ttu-id="4c293-152">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="4c293-153">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="4c293-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="4c293-154">プロジェクト フォルダーに移動し、プロジェクトのソリューション ファイル ( *.sln*) を開いてプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="4c293-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4c293-155">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c293-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4c293-156">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="4c293-157">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4c293-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c293-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="4c293-159">**ソリューション エクスプローラー**で、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4c293-160">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が *nuget.org* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4c293-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="4c293-161">**[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4c293-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="4c293-162">検索結果で、[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="4c293-163">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="4c293-164">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4c293-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4c293-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="4c293-166">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4c293-167">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4c293-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4c293-168">**[ソリューション]** サイド バーで、 **[BlazorSignalRApp.Client]** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="4c293-169">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが *nuget.org* に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="4c293-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="4c293-170">**[参照]** を選択した状態で、検索ボックスに「Microsoft.AspNetCore.SignalR.Client」と入力します。</span><span class="sxs-lookup"><span data-stu-id="4c293-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="4c293-171">検索結果で、[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="4c293-172">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4c293-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c293-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="4c293-174">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="4c293-175">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-175">Add a SignalR hub</span></span>

<span data-ttu-id="4c293-176">**BlazorSignalRApp.Server** プロジェクトで、*Hubs* (複数形) フォルダーを作成し、次の `ChatHub` クラスを追加します (*Hubs/ChatHub.cs*)。</span><span class="sxs-lookup"><span data-stu-id="4c293-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="4c293-177">サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="4c293-178">**BlazorSignalRApp.Server** プロジェクトで、*Startup.cs* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="4c293-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="4c293-179">ファイルの先頭に `ChatHub` クラスの名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="4c293-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="4c293-180">SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="4c293-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="4c293-181">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="4c293-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="4c293-182">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="4c293-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="4c293-183">コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="4c293-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="4c293-184">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="4c293-185">**BlazorSignalRApp.Client** プロジェクトで、*Pages/Index.razor* ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="4c293-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="4c293-186">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4c293-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="4c293-187">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="4c293-187">Run the app</span></span>

1. <span data-ttu-id="4c293-188">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="4c293-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4c293-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4c293-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4c293-190">**ソリューション エクスプローラー**で、**BlazorSignalRApp.Server** プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="4c293-191"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4c293-192">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4c293-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4c293-193">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="4c293-194">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4c293-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="4c293-195">交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた ![SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="4c293-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="4c293-196">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4c293-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="4c293-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4c293-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4c293-198">VS Code により、サーバー アプリの起動プロファイルの作成が提案される場合 ( *.vscode/launch.json*)、`program` エントリが次のように表示され、アプリのアセンブリをポイントします (`{APPLICATION NAME}.Server.dll`)。</span><span class="sxs-lookup"><span data-stu-id="4c293-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="4c293-199"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4c293-200">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4c293-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4c293-201">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="4c293-202">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4c293-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="4c293-203">交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた ![SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="4c293-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="4c293-204">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4c293-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4c293-205">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="4c293-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="4c293-206">**[ソリューション]** サイド バーで、**BlazorSignalRApp.Server** プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="4c293-207"><kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="4c293-208">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4c293-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4c293-209">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="4c293-210">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4c293-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="4c293-211">交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた ![SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="4c293-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="4c293-212">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4c293-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4c293-213">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4c293-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="4c293-214">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4c293-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="4c293-215">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="4c293-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4c293-216">いずれかのブラウザーを選択し、名前とメッセージを入力し、 **[送信]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="4c293-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="4c293-217">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="4c293-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="4c293-218">交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた ![SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="4c293-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="4c293-219">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="4c293-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="4c293-220">次の手順</span><span class="sxs-lookup"><span data-stu-id="4c293-220">Next steps</span></span>

<span data-ttu-id="4c293-221">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="4c293-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4c293-222">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="4c293-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="4c293-223">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="4c293-224">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="4c293-225">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="4c293-226">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="4c293-226">Add Razor component code for chat</span></span>

<span data-ttu-id="4c293-227">Blazor アプリの構築について詳しくは、Blazor のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4c293-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="4c293-228">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4c293-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="4c293-229">[認証のための SignalR のクロスオリジンネゴシエーション](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="4c293-229">[SignalR cross-origin negotiation for authentication](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)</span></span>
