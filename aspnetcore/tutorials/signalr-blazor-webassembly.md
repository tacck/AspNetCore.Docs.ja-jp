---
title: 'ASP.NET Core :::no-loc(SignalR)::: を :::no-loc(Blazor WebAssembly)::: と共に使用する'
author: guardrex
description: ':::no-loc(Blazor WebAssembly)::: で ASP.NET Core :::no-loc(SignalR)::: を使用するチャット アプリを作成します。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 6086c422b2d0598af309bbf8b900b9e03a3fe147
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491588"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="5c80d-103">ASP.NET Core :::no-loc(SignalR)::: を :::no-loc(Blazor WebAssembly)::: と共に使用する</span><span class="sxs-lookup"><span data-stu-id="5c80d-103">Use ASP.NET Core :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="5c80d-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5c80d-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5c80d-105">このチュートリアルでは、:::no-loc(Blazor WebAssembly)::: と共に :::no-loc(SignalR)::: を使用してリアルタイム アプリをビルドするための基礎について説明します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-105">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="5c80d-106">以下の方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5c80d-107">:::no-loc(Blazor WebAssembly)::: でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="5c80d-107">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="5c80d-108">:::no-loc(SignalR)::: クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-108">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="5c80d-109">:::no-loc(SignalR)::: ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-109">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="5c80d-110">:::no-loc(SignalR)::: サービスと :::no-loc(SignalR)::: ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-110">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="5c80d-111">チャット用の :::no-loc(Razor)::: コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-111">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="5c80d-112">このチュートリアルの最後には、動作するチャット アプリが完成します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="5c80d-113">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="5c80d-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5c80d-114">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5c80d-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="5c80d-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c80d-115">Visual Studio</span></span>](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* <span data-ttu-id="5c80d-116">**ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.8 以降 (プレビュー段階)](https://visualstudio.microsoft.com/vs/preview/)</span><span class="sxs-lookup"><span data-stu-id="5c80d-116">[Visual Studio 2019 16.8 or later (in preview)](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c80d-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c80d-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c80d-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c80d-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* <span data-ttu-id="5c80d-119">[Visual Studio for Mac バージョン 8.8 以降 (プレビュー段階)](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)。</span><span class="sxs-lookup"><span data-stu-id="5c80d-119">[Visual Studio for Mac version 8.8 or later (in preview)](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="5c80d-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c80d-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="5c80d-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c80d-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5c80d-122">**ASP.NET および Web 開発** ワークロードを含む [Visual Studio 2019 16.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="5c80d-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c80d-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c80d-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c80d-124">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c80d-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="5c80d-125">Visual Studio for Mac バージョン 8.6 以降</span><span class="sxs-lookup"><span data-stu-id="5c80d-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="5c80d-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c80d-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="5c80d-127">ホストされる :::no-loc(Blazor WebAssembly)::: アプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="5c80d-127">Create a hosted :::no-loc(Blazor WebAssembly)::: app project</span></span>

<span data-ttu-id="5c80d-128">使用するツールに向けたガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="5c80d-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c80d-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c80d-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5c80d-130">Visual Studio 16.8 以降と .NET Core SDK 5.0.0 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="5c80d-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="5c80d-131">Visual Studio 16.6 以降と .NET Core SDK 3.1.300 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="5c80d-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="5c80d-132">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-132">Create a new project.</span></span>

1. <span data-ttu-id="5c80d-133">**[:::no-loc(Blazor)::: アプリ]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="5c80d-133">Select **:::no-loc(Blazor)::: App** and select **Next**.</span></span>

1. <span data-ttu-id="5c80d-134">**[プロジェクト名]** フィールドに「`:::no-loc(Blazor)::::::no-loc(SignalR):::App`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-134">Type `:::no-loc(Blazor)::::::no-loc(SignalR):::App` in the **Project name** field.</span></span> <span data-ttu-id="5c80d-135">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5c80d-136">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-136">Select **Create**.</span></span>

1. <span data-ttu-id="5c80d-137">**:::no-loc(Blazor WebAssembly)::: アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-137">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span>

1. <span data-ttu-id="5c80d-138">**[詳細設定]** で、 **[ASP.NET Core hosted]\(ASP.NET Core でホストされる\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="5c80d-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="5c80d-139">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c80d-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c80d-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5c80d-141">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
   ```

1. <span data-ttu-id="5c80d-142">Visual Studio Code で、アプリのプロジェクト フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="5c80d-143">アプリをビルドおよびデバッグするためのアセットを追加するダイアログが表示されたら、 **[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="5c80d-144">Visual Studio Code では、生成された `launch.json` ファイルと `tasks.json` ファイルが含まれる `.vscode` フォルダーが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c80d-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c80d-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c80d-146">最新バージョンの [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) をインストールし、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="5c80d-147">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から **新しい** プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="5c80d-148">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="5c80d-149">**:::no-loc(Blazor WebAssembly)::: アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-149">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="5c80d-150">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-150">Select **Next**.</span></span>

1. <span data-ttu-id="5c80d-151">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="5c80d-152">**[ASP.NET Core Hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="5c80d-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="5c80d-153">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-153">Select **Next**.</span></span>

1. <span data-ttu-id="5c80d-154">**[プロジェクト名]** フィールドで、アプリに `:::no-loc(Blazor)::::::no-loc(SignalR):::App` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-154">In the **Project Name** field, name the app `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span></span> <span data-ttu-id="5c80d-155">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-155">Select **Create**.</span></span>

   <span data-ttu-id="5c80d-156">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="5c80d-157">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="5c80d-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="5c80d-158">プロジェクト フォルダーに移動し、プロジェクトのソリューション ファイル (`.sln`) を開いてプロジェクトを開きます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c80d-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c80d-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5c80d-160">コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="5c80d-161">:::no-loc(SignalR)::: クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-161">Add the :::no-loc(SignalR)::: client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c80d-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c80d-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="5c80d-163">**ソリューション エクスプローラー** で、`:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-163">In **Solution Explorer** , right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5c80d-164">**[NuGet パッケージの管理]** ダイアログで、 **[パッケージ ソース]** が `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="5c80d-165">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-165">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="5c80d-166">検索結果から [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) パッケージを選択し、 **[インストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-166">In the search results, select the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="5c80d-167">**[変更のプレビュー]** ダイアログ ボックスが表示されたら、 **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="5c80d-168">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c80d-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c80d-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="5c80d-170">**統合ターミナル** (ツール バーの **[表示]**  >  **[ターミナル]** ) で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c80d-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c80d-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c80d-172">**[ソリューション]** サイドバーで、`:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-172">In the **Solution** sidebar, right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5c80d-173">**[NuGet パッケージの管理]** ダイアログで、ソースのドロップダウンが `nuget.org` に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="5c80d-174">**[参照]** が選択されている状態で、検索ボックスに「`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-174">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="5c80d-175">検索結果の [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) パッケージの横にあるチェック ボックスをオンにし、 **[パッケージの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="5c80d-176">**[ライセンスの同意]** ダイアログが表示されたら、ライセンス条項に同意する場合は **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c80d-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c80d-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="5c80d-178">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd :::no-loc(Blazor)::::::no-loc(SignalR):::App
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="5c80d-179">:::no-loc(SignalR)::: ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-179">Add a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="5c80d-180">`:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` プロジェクトで、`Hubs` (複数形) フォルダーを作成し、次の `ChatHub` クラス (`Hubs/ChatHub.cs`) を追加します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-180">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="5c80d-181">サービスと :::no-loc(SignalR)::: ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-181">Add services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>

1. <span data-ttu-id="5c80d-182">`:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` プロジェクトで、`Startup.cs` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-182">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="5c80d-183">ファイルの先頭に `ChatHub` クラスの名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using :::no-loc(Blazor)::::::no-loc(SignalR):::App.Server.Hubs;
   ```

1. <span data-ttu-id="5c80d-184">:::no-loc(SignalR)::: および応答の圧縮ミドルウェア サービスを `Startup.ConfigureServices` に追加します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-184">Add :::no-loc(SignalR)::: and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="5c80d-185">`Startup.Configure`の場合:</span><span class="sxs-lookup"><span data-stu-id="5c80d-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="5c80d-186">処理パイプラインの構成の上部にある応答圧縮ミドルウェアを使用します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="5c80d-187">コントローラーのエンドポイントとクライアント側のフォールバックのエンドポイントの間に、ハブのエンドポイントを追加します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="5c80d-188">チャット用の :::no-loc(Razor)::: コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-188">Add :::no-loc(Razor)::: component code for chat</span></span>

1. <span data-ttu-id="5c80d-189">`:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` プロジェクトで、`Pages/Index.razor` ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-189">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="5c80d-190">マークアップを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="5c80d-191">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="5c80d-191">Run the app</span></span>

1. <span data-ttu-id="5c80d-192">お使いのツール用のガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="5c80d-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c80d-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c80d-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="5c80d-194">**ソリューション エクスプローラー** で `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-194">In **Solution Explorer** , select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="5c80d-195"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c80d-196">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c80d-197">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c80d-198">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-198">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="5c80d-200">引用: *Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c80d-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5c80d-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c80d-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="5c80d-202">VS Code により、サーバー アプリの起動プロファイルの作成が提案される場合 (`.vscode/launch.json`)、`program` エントリが次のように表示され、アプリのアセンブリをポイントします (`{APPLICATION NAME}.Server.dll`)。</span><span class="sxs-lookup"><span data-stu-id="5c80d-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="5c80d-203"><kbd>F5</kbd> キーを押して、デバッグしてアプリを実行するか、<kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c80d-204">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c80d-205">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c80d-206">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-206">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="5c80d-208">引用: *Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c80d-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5c80d-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5c80d-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="5c80d-210">**[ソリューション]** サイドバーで、`:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-210">In the **Solution** sidebar, select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="5c80d-211"><kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグしてアプリを実行するか、<kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> を押して、デバッグなしでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="5c80d-212">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c80d-213">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c80d-214">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-214">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="5c80d-216">引用: *Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c80d-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c80d-217">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c80d-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="5c80d-218">コマンド シェルで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="5c80d-219">アドレス バーから URL をコピーし、別のブラウザー インスタンスまたはタブを開き、アドレス バーに URL を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="5c80d-220">いずれかのブラウザーを選択し、名前とメッセージを入力し、メッセージを送信するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="5c80d-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="5c80d-221">両方のページに、その名前とメッセージが瞬時に表示されます。</span><span class="sxs-lookup"><span data-stu-id="5c80d-221">The name and message are displayed on both pages instantly:</span></span>

   ![交換されたメッセージを示す、2 つのブラウザー ウィンドウで開かれた SignalR Blazor WebAssembly サンプル アプリ。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="5c80d-223">引用: *Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="5c80d-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="5c80d-224">次の手順</span><span class="sxs-lookup"><span data-stu-id="5c80d-224">Next steps</span></span>

<span data-ttu-id="5c80d-225">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="5c80d-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5c80d-226">:::no-loc(Blazor WebAssembly)::: でホストされるアプリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="5c80d-226">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="5c80d-227">:::no-loc(SignalR)::: クライアント ライブラリを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-227">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="5c80d-228">:::no-loc(SignalR)::: ハブを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-228">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="5c80d-229">:::no-loc(SignalR)::: サービスと :::no-loc(SignalR)::: ハブのエンドポイントを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-229">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="5c80d-230">チャット用の :::no-loc(Razor)::: コンポーネント コードを追加する</span><span class="sxs-lookup"><span data-stu-id="5c80d-230">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="5c80d-231">:::no-loc(Blazor)::: アプリの構築について詳しくは、:::no-loc(Blazor)::: のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5c80d-231">To learn more about building :::no-loc(Blazor)::: apps, see the :::no-loc(Blazor)::: documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="5c80d-232"><xref:blazor/index>
> [:::no-loc(Identity)::: Server、WebSockets、Server-Sent Events によるベアラー トークン認証](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="5c80d-232"><xref:blazor/index>
[Bearer token authentication with :::no-loc(Identity)::: Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c80d-233">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5c80d-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="5c80d-234">認証のための :::no-loc(SignalR)::: のクロスオリジンネゴシエーション</span><span class="sxs-lookup"><span data-stu-id="5c80d-234">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
