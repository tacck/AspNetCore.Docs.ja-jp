---
title: ASP.NET Core SignalR を Blazor WebAssembly と共に使用する
author: guardrex
description: Blazor WebAssembly で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: d5aa7520a637b18e014519134dfe2d2139e7c11d
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147775"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="f8ac9-103">ASP.NET Core SignalR を Blazor WebAssembly と共に使用する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="f8ac9-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f8ac9-105">このチュートリアルでは、Blazor WebAssembly と共に SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="f8ac9-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f8ac9-107">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="f8ac9-108">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="f8ac9-109">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="f8ac9-110">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f8ac9-111">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-111">Add Razor component code for chat</span></span>

<span data-ttu-id="f8ac9-112">このチュートリアルの最後には、動作するチャット アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="f8ac9-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f8ac9-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="f8ac9-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8ac9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8ac9-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f8ac9-116">**ASP.NET および Web 開発**ワークロードを含む [Visual Studio 2019 16.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8ac9-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8ac9-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8ac9-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f8ac9-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="f8ac9-119">Visual Studio for Mac バージョン 8.6 以降</span><span class="sxs-lookup"><span data-stu-id="f8ac9-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f8ac9-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f8ac9-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="f8ac9-121">ホストされる Blazor WebAssembly アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="f8ac9-122">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8ac9-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8ac9-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="f8ac9-124">Visual Studio 16.6 以降と .NET Core SDK 3.1.300 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="f8ac9-125">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-125">Create a new project.</span></span>

1. <span data-ttu-id="f8ac9-126">**[Blazor アプリ]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="f8ac9-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="f8ac9-127">**[プロジェクト名]** フィールドに「`BlazorSignalRApp`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="f8ac9-128">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f8ac9-129">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-129">Select **Create**.</span></span>

1. <span data-ttu-id="f8ac9-130">**Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="f8ac9-131">**[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f8ac9-132">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8ac9-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8ac9-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f8ac9-134">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="f8ac9-135">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="f8ac9-136">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="f8ac9-137">Visual Studio Code では、生成された `launch.json` ファイルと `tasks.json` ファイルが含まれる `.vscode` フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8ac9-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f8ac9-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f8ac9-139">最新バージョンの [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) をインストールし、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="f8ac9-140">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から**新しい**プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f8ac9-141">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="f8ac9-142">**Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f8ac9-143">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-143">Select **Next**.</span></span>

1. <span data-ttu-id="f8ac9-144">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-144">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f8ac9-145">**[ASP.NET Core Hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-145">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="f8ac9-146">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-146">Select **Next**.</span></span>

1. <span data-ttu-id="f8ac9-147">**[プロジェクト名]** フィールドで、アプリに `BlazorSignalRApp` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-147">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="f8ac9-148">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-148">Select **Create**.</span></span>

   <span data-ttu-id="f8ac9-149">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f8ac9-150">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-150">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="f8ac9-151">プロジェクト フォルダーに移動し、プロジェクトのソリューション ファイル (`.sln`) を開いてプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-151">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f8ac9-152">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f8ac9-152">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f8ac9-153">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-153">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f8ac9-154">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-154">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8ac9-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8ac9-155">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f8ac9-156">**ソリューション エクスプローラー**で、`BlazorSignalRApp.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-156">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f8ac9-157">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-157">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f8ac9-158">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-158">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f8ac9-159">検索結果から [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-159">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="f8ac9-160">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-160">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f8ac9-161">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-161">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8ac9-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8ac9-162">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f8ac9-163">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-163">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8ac9-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f8ac9-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f8ac9-165">**[ソリューション]** サイドバーで、`BlazorSignalRApp.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-165">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f8ac9-166">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-166">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="f8ac9-167">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-167">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="f8ac9-168">検索結果の [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-168">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="f8ac9-169">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-169">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f8ac9-170">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f8ac9-170">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f8ac9-171">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-171">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="f8ac9-172">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-172">Add a SignalR hub</span></span>

<span data-ttu-id="f8ac9-173">`BlazorSignalRApp.Server` プロジェクトで、`Hubs` (複数形) フォルダーを作成し、次の `ChatHub` クラス (`Hubs/ChatHub.cs`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-173">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="f8ac9-174">サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-174">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="f8ac9-175">`BlazorSignalRApp.Server` プロジェクトで、`Startup.cs` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-175">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="f8ac9-176">ファイルの先頭に `ChatHub` クラスの名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-176">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="f8ac9-177">SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-177">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="f8ac9-178">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="f8ac9-178">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="f8ac9-179">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-179">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="f8ac9-180">コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-180">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="f8ac9-181">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-181">Add Razor component code for chat</span></span>

1. <span data-ttu-id="f8ac9-182">`BlazorSignalRApp.Client` プロジェクトで、`Pages/Index.razor` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-182">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="f8ac9-183">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-183">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="f8ac9-184">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-184">Run the app</span></span>

1. <span data-ttu-id="f8ac9-185">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-185">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8ac9-186">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8ac9-186">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f8ac9-187">**ソリューション エクスプローラー**で `BlazorSignalRApp.Server` プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-187">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="f8ac9-188"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-188">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f8ac9-189">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-189">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f8ac9-190">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-190">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f8ac9-191">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-191">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="f8ac9-192">交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた ![SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-192">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="f8ac9-193">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-193">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8ac9-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8ac9-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f8ac9-195">VS Code により、サーバー アプリの起動プロファイルの作成が提案される場合 (`.vscode/launch.json`)、`program` エントリが次のように表示され、アプリのアセンブリをポイントします (`{APPLICATION NAME}.Server.dll`)。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-195">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="f8ac9-196"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-196">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f8ac9-197">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-197">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f8ac9-198">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-198">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f8ac9-199">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-199">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="f8ac9-200">交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた ![SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-200">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="f8ac9-201">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-201">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8ac9-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="f8ac9-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f8ac9-203">**[ソリューション]** サイドバーで、`BlazorSignalRApp.Server` プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-203">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="f8ac9-204"><kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-204">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f8ac9-205">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-205">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f8ac9-206">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-206">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f8ac9-207">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-207">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="f8ac9-208">交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた ![SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-208">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="f8ac9-209">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-209">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f8ac9-210">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f8ac9-210">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f8ac9-211">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-211">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="f8ac9-212">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f8ac9-213">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="f8ac9-214">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-214">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="f8ac9-215">交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた ![SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-215">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="f8ac9-216">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="f8ac9-217">次の手順</span><span class="sxs-lookup"><span data-stu-id="f8ac9-217">Next steps</span></span>

<span data-ttu-id="f8ac9-218">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-218">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f8ac9-219">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-219">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="f8ac9-220">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-220">Add the SignalR client library</span></span>
> * <span data-ttu-id="f8ac9-221">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-221">Add a SignalR hub</span></span>
> * <span data-ttu-id="f8ac9-222">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-222">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f8ac9-223">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="f8ac9-223">Add Razor component code for chat</span></span>

<span data-ttu-id="f8ac9-224">Blazor アプリの構築について詳しくは、Blazor のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="f8ac9-224">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="f8ac9-225">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f8ac9-225">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="f8ac9-226">[認証のための SignalR のクロスオリジンネゴシエーション](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="f8ac9-226">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
