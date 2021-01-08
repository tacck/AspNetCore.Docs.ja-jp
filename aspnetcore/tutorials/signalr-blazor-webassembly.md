---
title: ホストされた Blazor WebAssembly アプリで ASP.NET Core SignalR を使用する
author: guardrex
description: Blazor WebAssembly で ASP.NET Core SignalR を使用するチャット アプリを作成します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
no-loc:
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797354"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="ec3a6-103">ホストされた Blazor WebAssembly アプリで ASP.NET Core SignalR を使用する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="ec3a6-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ec3a6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ec3a6-105">このチュートリアルでは、Blazor WebAssembly と共に SignalR を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="ec3a6-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ec3a6-107">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="ec3a6-108">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="ec3a6-109">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="ec3a6-110">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="ec3a6-111">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-111">Add Razor component code for chat</span></span>

<span data-ttu-id="ec3a6-112">このチュートリアルの最後には、動作するチャット アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="ec3a6-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec3a6-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ec3a6-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="ec3a6-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec3a6-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec3a6-116">**ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.8 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="ec3a6-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec3a6-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec3a6-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ec3a6-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec3a6-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ec3a6-119">Visual Studio for Mac バージョン 8.8 以降</span><span class="sxs-lookup"><span data-stu-id="ec3a6-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="ec3a6-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ec3a6-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="ec3a6-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec3a6-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec3a6-122">**ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="ec3a6-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec3a6-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec3a6-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ec3a6-124">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec3a6-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ec3a6-125">Visual Studio for Mac バージョン 8.6 以降</span><span class="sxs-lookup"><span data-stu-id="ec3a6-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="ec3a6-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ec3a6-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="ec3a6-127">ホストされる Blazor WebAssembly アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="ec3a6-128">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec3a6-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec3a6-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ec3a6-130">Visual Studio 16.8 以降と .NET Core SDK 5.0.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ec3a6-131">Visual Studio 16.6 以降と .NET Core SDK 3.1.300 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="ec3a6-132">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-132">Create a new project.</span></span>

1. <span data-ttu-id="ec3a6-133">**[Blazor アプリ]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="ec3a6-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="ec3a6-134">**[プロジェクト名]** フィールドに「`BlazorSignalRApp`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="ec3a6-135">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ec3a6-136">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-136">Select **Create**.</span></span>

1. <span data-ttu-id="ec3a6-137">**Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="ec3a6-138">**[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="ec3a6-139">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec3a6-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec3a6-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ec3a6-141">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="ec3a6-142">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="ec3a6-143">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="ec3a6-144">Visual Studio Code では、生成された `launch.json` ファイルと `tasks.json` ファイルが含まれる `.vscode` フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ec3a6-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec3a6-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ec3a6-146">最新バージョンの [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) をインストールし、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="ec3a6-147">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から **新しい** プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="ec3a6-148">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="ec3a6-149">**Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ec3a6-150">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-150">Select **Next**.</span></span>

1. <span data-ttu-id="ec3a6-151">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="ec3a6-152">**[ASP.NET Core Hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="ec3a6-153">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-153">Select **Next**.</span></span>

1. <span data-ttu-id="ec3a6-154">**[プロジェクト名]** フィールドで、アプリに `BlazorSignalRApp` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="ec3a6-155">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-155">Select **Create**.</span></span>

   <span data-ttu-id="ec3a6-156">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="ec3a6-157">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="ec3a6-158">プロジェクト フォルダーに移動し、プロジェクトのソリューション ファイル (`.sln`) を開いてプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ec3a6-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ec3a6-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ec3a6-160">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="ec3a6-161">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec3a6-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec3a6-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ec3a6-163">**ソリューション エクスプローラー** で、`BlazorSignalRApp.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ec3a6-164">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ec3a6-165">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ec3a6-166">検索結果から [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="ec3a6-167">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ec3a6-168">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec3a6-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec3a6-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ec3a6-170">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ec3a6-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec3a6-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ec3a6-172">**[ソリューション]** サイドバーで、`BlazorSignalRApp.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ec3a6-173">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ec3a6-174">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.SignalR.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ec3a6-175">検索結果の [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="ec3a6-176">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ec3a6-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ec3a6-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ec3a6-178">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="ec3a6-179">System.Text.Encodings.Web パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="ec3a6-180">ASP.NET Core 3.1 アプリで [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 を使用する場合のパッケージの解決に関する問題のため、`BlazorSignalRApp.Server` プロジェクトで [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) のパッケージ参照が必要になります。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="ec3a6-181">基になる問題は、.NET 5 の今後のパッチ リリースで解決される予定です。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="ec3a6-182">詳細については、「[System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="ec3a6-183">ASP.NET Core 3.1 でホストされている Blazor ソリューションの `BlazorSignalRApp.Server` プロジェクトに [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) を追加するには、使用するツールのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec3a6-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec3a6-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ec3a6-185">**ソリューション エクスプローラー** で、`BlazorSignalRApp.Server` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ec3a6-186">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ec3a6-187">**[参照]** が選択されている状態で、検索ボックスに「`System.Text.Encodings.Web`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ec3a6-188">検索結果から [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="ec3a6-189">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ec3a6-190">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec3a6-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec3a6-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ec3a6-192">**統合ターミナル** (ツール バーの **[表示]** > **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ec3a6-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec3a6-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ec3a6-194">**[ソリューション]** サイドバーで、`BlazorSignalRApp.Server` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ec3a6-195">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ec3a6-196">**[参照]** が選択されている状態で、検索ボックスに「`System.Text.Encodings.Web`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ec3a6-197">検索結果の [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="ec3a6-198">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ec3a6-199">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ec3a6-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ec3a6-200">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="ec3a6-201">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-201">Add a SignalR hub</span></span>

<span data-ttu-id="ec3a6-202">`BlazorSignalRApp.Server` プロジェクトで、`Hubs` (複数形) フォルダーを作成し、次の `ChatHub` クラス (`Hubs/ChatHub.cs`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="ec3a6-203">サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="ec3a6-204">`BlazorSignalRApp.Server` プロジェクトで、`Startup.cs` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="ec3a6-205">ファイルの先頭に `ChatHub` クラスの名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ec3a6-206">SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="ec3a6-207">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="ec3a6-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ec3a6-208">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ec3a6-209">コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ec3a6-210">SignalR および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="ec3a6-211">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="ec3a6-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ec3a6-212">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ec3a6-213">コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="ec3a6-214">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="ec3a6-215">`BlazorSignalRApp.Client` プロジェクトで、`Pages/Index.razor` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ec3a6-216">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ec3a6-217">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="ec3a6-218">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-218">Run the app</span></span>

<span data-ttu-id="ec3a6-219">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec3a6-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec3a6-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ec3a6-221">**ソリューション エクスプローラー** で `BlazorSignalRApp.Server` プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="ec3a6-222"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ec3a6-223">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ec3a6-224">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ec3a6-225">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-225">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="ec3a6-227">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ec3a6-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec3a6-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec3a6-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ec3a6-229">VS Code により、サーバー アプリの起動プロファイルの作成が提案される場合 (`.vscode/launch.json`)、`program` エントリが次のように表示され、アプリのアセンブリをポイントします (`{APPLICATION NAME}.Server.dll`)。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-229">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ec3a6-230">VS Code により、サーバー アプリの起動プロファイルの作成が提案される場合 (`.vscode/launch.json`)、`program` エントリが次のように表示され、アプリのアセンブリをポイントします (`{APPLICATION NAME}.Server.dll`)。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-230">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="ec3a6-231"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-231">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ec3a6-232">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-232">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ec3a6-233">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-233">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ec3a6-234">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-234">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="ec3a6-236">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ec3a6-236">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ec3a6-237">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="ec3a6-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ec3a6-238">**[ソリューション]** サイドバーで、`BlazorSignalRApp.Server` プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-238">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="ec3a6-239"><kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-239">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ec3a6-240">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ec3a6-241">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ec3a6-242">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-242">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="ec3a6-244">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ec3a6-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ec3a6-245">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ec3a6-245">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ec3a6-246">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-246">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="ec3a6-247">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ec3a6-248">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ec3a6-249">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-249">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="ec3a6-251">引用:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ec3a6-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="ec3a6-252">次の手順</span><span class="sxs-lookup"><span data-stu-id="ec3a6-252">Next steps</span></span>

<span data-ttu-id="ec3a6-253">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-253">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ec3a6-254">Blazor WebAssembly でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-254">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="ec3a6-255">SignalR クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-255">Add the SignalR client library</span></span>
> * <span data-ttu-id="ec3a6-256">SignalR ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-256">Add a SignalR hub</span></span>
> * <span data-ttu-id="ec3a6-257">SignalR サービスと SignalR ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-257">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="ec3a6-258">チャット用の Razor コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="ec3a6-258">Add Razor component code for chat</span></span>

<span data-ttu-id="ec3a6-259">Blazor アプリの構築について詳しくは、Blazor のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec3a6-259">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="ec3a6-260"><xref:blazor/index>
> [Identity Server、WebSockets、Server-Sent Events によるベアラー トークン認証](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="ec3a6-260"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec3a6-261">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ec3a6-261">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="ec3a6-262">認証のための SignalR のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="ec3a6-262">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
