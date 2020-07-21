---
title: ASP.NET Core Blazor 用のツール
author: guardrex
description: Blazor アプリのビルドに使用できるツールについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 33245e669b317ed577a8a1652b2eed8f9ea5b915
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407646"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="a60a8-103">ASP.NET Core Blazor 用のツール</span><span class="sxs-lookup"><span data-stu-id="a60a8-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="a60a8-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a60a8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="os-windows"

1. <span data-ttu-id="a60a8-105">**ASP.NET および Web 開発**ワークロードと共に、最新バージョンの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールする</span><span class="sxs-lookup"><span data-stu-id="a60a8-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="a60a8-106">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-106">Create a new project.</span></span>

1. <span data-ttu-id="a60a8-107">**[Blazor アプリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-107">Select **Blazor App**.</span></span> <span data-ttu-id="a60a8-108">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-108">Select **Next**.</span></span>

1. <span data-ttu-id="a60a8-109">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a60a8-110">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="a60a8-111">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-111">Select **Create**.</span></span>

1. <span data-ttu-id="a60a8-112">Blazor WebAssembly エクスペリエンスの場合は、 **Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a60a8-113">Blazor Server エクスペリエンスの場合は、 **Blazor Server アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a60a8-114">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-114">Select **Create**.</span></span>

   <span data-ttu-id="a60a8-115">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a60a8-115">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a60a8-116"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="a60a8-117">ASP.NET Core HTTPS 開発証明書の信頼の詳細については、「<xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a60a8-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="os-linux"

1. <span data-ttu-id="a60a8-118">最新バージョンの [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a60a8-118">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="a60a8-119">以前に SDK をインストールした場合、コマンド シェルでコマンドを実行し、インストールされているバージョンを判断できます。</span><span class="sxs-lookup"><span data-stu-id="a60a8-119">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="a60a8-120">[Visual Studio Code](https://code.visualstudio.com/) の最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a60a8-120">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="a60a8-121">最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a60a8-121">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="a60a8-122">Blazor WebAssembly エクスペリエンスの場合は、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-122">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="a60a8-123">Blazor Server エクスペリエンスの場合は、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-123">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="a60a8-124">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a60a8-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a60a8-125">Visual Studio Code で `WebApplication1` フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="a60a8-125">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="a60a8-126">IDE によって、プロジェクトをビルドおよびデバッグするためにアセットを追加するよう要求されます。</span><span class="sxs-lookup"><span data-stu-id="a60a8-126">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="a60a8-127">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-127">Select **Yes**.</span></span>

1. <span data-ttu-id="a60a8-128"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-128">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="a60a8-129">開発証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="a60a8-129">Trust a development certificate</span></span>

<span data-ttu-id="a60a8-130">Linux で証明書を信頼するための一元的な方法はありません。</span><span class="sxs-lookup"><span data-stu-id="a60a8-130">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="a60a8-131">通常、次のいずれかの方法が採用されます。</span><span class="sxs-lookup"><span data-stu-id="a60a8-131">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="a60a8-132">ブラウザーの除外リストでアプリの URL を除外します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-132">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="a60a8-133">`localhost` に対するすべての自己署名証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-133">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="a60a8-134">ブラウザーの信頼された証明書の一覧に証明書を追加します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-134">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="a60a8-135">詳細については、お使いのブラウザーおよび Linux ディストリビューションで提供されているガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a60a8-135">For more information, see the guidance provided by your browser and Linux distribution.</span></span>

::: zone-end

::: zone pivot="os-macos"

1. <span data-ttu-id="a60a8-136">[Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="a60a8-136">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="a60a8-137">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から**新しい**プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-137">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="a60a8-138">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-138">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="a60a8-139">Blazor WebAssembly エクスペリエンスの場合は、 **Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-139">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="a60a8-140">Blazor Server エクスペリエンスの場合は、 **Blazor Server アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-140">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="a60a8-141">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-141">Select **Next**.</span></span>

   <span data-ttu-id="a60a8-142">2 つの Blazor ホスティング モデルである *Blazor WebAssembly* と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a60a8-142">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="a60a8-143">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-143">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="a60a8-144">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-144">Select **Next**.</span></span>

1. <span data-ttu-id="a60a8-145">**[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="a60a8-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="a60a8-146">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-146">Select **Create**.</span></span>

1. <span data-ttu-id="a60a8-147">*デバッガーを使用せずに*アプリを実行するには、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="a60a8-148">**[実行]**  > 、 **[デバッグの開始]** か [実行] (&#9654;) ボタンでアプリを実行すると、"*デバッガーなしで*" アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="a60a8-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="a60a8-149">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="a60a8-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="a60a8-150">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="a60a8-150">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="a60a8-151">ASP.NET Core HTTPS 開発証明書の信頼の詳細については、「<xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a60a8-151">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end
