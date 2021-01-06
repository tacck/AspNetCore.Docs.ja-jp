---
title: ASP.NET Core Blazor 用のツール
author: guardrex
description: Blazor アプリのビルドに使用できるツールについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 4813668f5278473fbaae36d572e69700b3fe771a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97764238"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="3a5a3-103">ASP.NET Core Blazor 用のツール</span><span class="sxs-lookup"><span data-stu-id="3a5a3-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="3a5a3-104">作成者: [Daniel Roth](https://github.com/danroth27)、[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a5a3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="3a5a3-105">**ASP.NET および Web 開発** ワークロードと共に、最新バージョンの [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) をインストールする</span><span class="sxs-lookup"><span data-stu-id="3a5a3-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="3a5a3-106">新しいプロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-106">Create a new project.</span></span>

1. <span data-ttu-id="3a5a3-107">**[Blazor アプリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-107">Select **Blazor App**.</span></span> <span data-ttu-id="3a5a3-108">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-108">Select **Next**.</span></span>

1. <span data-ttu-id="3a5a3-109">**[プロジェクト名]** フィールドにプロジェクト名を入力するか、既定のプロジェクト名をそのまま使用します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="3a5a3-110">**[場所]** エントリが正しいことを確認します。または、プロジェクトの場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="3a5a3-111">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-111">Select **Create**.</span></span>

1. <span data-ttu-id="3a5a3-112">Blazor WebAssembly エクスペリエンスの場合は、 **Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3a5a3-113">Blazor Server エクスペリエンスの場合は、 **Blazor Server アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3a5a3-114">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-114">Select **Create**.</span></span>

   <span data-ttu-id="3a5a3-115">ホステッド Blazor WebAssembly エクスペリエンスの場合は、 **[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="3a5a3-116">2 つの Blazor ホスティング モデル、 *Blazor WebAssembly* (スタンドアロンとホステッド) と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a5a3-117"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="3a5a3-118">ASP.NET Core HTTPS 開発証明書の信頼の詳細については、「<xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="3a5a3-119">最新バージョンの [.NET Core SDK](https://dotnet.microsoft.com/download) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="3a5a3-120">以前に SDK をインストールした場合、コマンド シェルでコマンドを実行し、インストールされているバージョンを判断できます。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="3a5a3-121">[Visual Studio Code](https://code.visualstudio.com) の最新版をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="3a5a3-122">最新の [C# for Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="3a5a3-123">Blazor WebAssembly エクスペリエンスの場合は、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="3a5a3-124">ホステッド Blazor WebAssembly エクスペリエンスの場合は、ホステッド オプション (`-ho` または `--hosted`) をコマンドに追加します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="3a5a3-125">Blazor Server エクスペリエンスの場合は、コマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="3a5a3-126">2 つの Blazor ホスティング モデル、 *Blazor WebAssembly* (スタンドアロンとホステッド) と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a5a3-127">Visual Studio Code で `WebApplication1` フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="3a5a3-128">IDE によって、プロジェクトをビルドおよびデバッグするためにアセットを追加するよう要求されます。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="3a5a3-129">**[はい]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-129">Select **Yes**.</span></span>

1. <span data-ttu-id="3a5a3-130"><kbd>Ctrl</kbd>+<kbd>F5</kbd> キーを押してアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="3a5a3-131">開発証明書を信頼する</span><span class="sxs-lookup"><span data-stu-id="3a5a3-131">Trust a development certificate</span></span>

<span data-ttu-id="3a5a3-132">Linux で証明書を信頼するための一元的な方法はありません。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="3a5a3-133">通常、次のいずれかの方法が採用されます。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="3a5a3-134">ブラウザーの除外リストでアプリの URL を除外します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="3a5a3-135">`localhost` に対するすべての自己署名証明書を信頼します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="3a5a3-136">ブラウザーの信頼された証明書の一覧に証明書を追加します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="3a5a3-137">詳細については、ご利用のブラウザーの製造元および Linux ディストリビューションによって提供されているガイダンスを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="3a5a3-138">[Visual Studio for Mac をインストールします](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="3a5a3-139">**[ファイル]**  >  **[新しいソリューション]** を選択するか、 **[スタート ウィンドウ]** から **新しい** プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="3a5a3-140">サイドバーで、 **[Web and Console]\(Web とコンソール\)**  >  **[アプリ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="3a5a3-141">Blazor WebAssembly エクスペリエンスの場合は、 **Blazor WebAssembly アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="3a5a3-142">Blazor Server エクスペリエンスの場合は、 **Blazor Server アプリ** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="3a5a3-143">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-143">Select **Next**.</span></span>

   <span data-ttu-id="3a5a3-144">2 つの Blazor ホスティング モデル、 *Blazor WebAssembly* (スタンドアロンとホステッド) と *Blazor Server* については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="3a5a3-145">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="3a5a3-146">**[次へ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-146">Select **Next**.</span></span>

1. <span data-ttu-id="3a5a3-147">ホステッド Blazor WebAssembly エクスペリエンスの場合は、 **[ASP.NET Core hosted]\(ASP.NET Core ホステッド\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="3a5a3-148">**[プロジェクト名]** フィールドで、アプリに `WebApplication1` という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="3a5a3-149">**[作成]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-149">Select **Create**.</span></span>

1. <span data-ttu-id="3a5a3-150">*デバッガーを使用せずに* アプリを実行するには、 **[実行]**  >  **[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="3a5a3-151">**[実行]**  > 、 **[デバッグの開始]** か [実行] (&#9654;) ボタンでアプリを実行すると、"*デバッガーなしで*" アプリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="3a5a3-152">開発証明書を信頼することを求めるメッセージが表示されたら、証明書を信頼して続行します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="3a5a3-153">証明書を信頼するには、ユーザーとキーチェーンのパスワードが必要です。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="3a5a3-154">ASP.NET Core HTTPS 開発証明書の信頼の詳細については、「<xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="3a5a3-155">クロスプラットフォームの Blazor 開発に Visual Studio Code を使用する</span><span class="sxs-lookup"><span data-stu-id="3a5a3-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="3a5a3-156">[Visual Studio Code](https://code.visualstudio.com/) は、Blazor アプリの開発に使用できるオープン ソースのクロスプラットフォーム統合開発環境 (IDE) です。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="3a5a3-157">.NET CLI を使用して、Visual Studio Code で開発用の新しい Blazor アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="3a5a3-158">詳細については、[この記事の Linux バージョン](/aspnet/core/blazor/tooling?pivots=linux)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-158">For more information, see the [Linux version of this article](/aspnet/core/blazor/tooling?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="3a5a3-159">Blazor テンプレート オプション</span><span class="sxs-lookup"><span data-stu-id="3a5a3-159">Blazor template options</span></span>

<span data-ttu-id="3a5a3-160">Blazor フレームワークには、Blazor ホスティング モデルのそれぞれに対して新しいアプリを作成するためのテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="3a5a3-161">テンプレートは、Blazor 開発用に選択したツール (Visual Studio、Visual Studio for Mac、Visual Studio Code、または .NET CLI) に関係なく、新しい Blazor プロジェクトとソリューションを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="3a5a3-162">Blazor WebAssembly プロジェクト テンプレート: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="3a5a3-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="3a5a3-163">Blazor Server プロジェクト テンプレート: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="3a5a3-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="3a5a3-164">Blazor のホスティング モデルの詳細については、「<xref:blazor/hosting-models>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="3a5a3-165">テンプレート オプションを使用するには、コマンド シェルで [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI コマンドにヘルプ オプション (`-h` または `--help`) を渡します。</span><span class="sxs-lookup"><span data-stu-id="3a5a3-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
