---
title: IIS に ASP.NET Core アプリを発行する
author: rick-anderson
description: IIS サーバーで ASP.NET Core アプリをホストする方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
no-loc:
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 40c47da472257862414ba33be582eb19d3f0b29c
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754555"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="6caff-103">IIS に ASP.NET Core アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="6caff-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="6caff-104">このチュートリアルでは、IIS サーバーで ASP.NET Core アプリをホストする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="6caff-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="6caff-105">このチュートリアルでは、次の件について取り上げます。</span><span class="sxs-lookup"><span data-stu-id="6caff-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6caff-106">.NET Core ホスティング バンドルを Windows Server にインストールします。</span><span class="sxs-lookup"><span data-stu-id="6caff-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="6caff-107">IIS サイトを IIS マネージャーで作成します。</span><span class="sxs-lookup"><span data-stu-id="6caff-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="6caff-108">ASP.NET Core アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="6caff-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6caff-109">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6caff-109">Prerequisites</span></span>

* <span data-ttu-id="6caff-110">開発用のコンピューターにインストールされている [.NET Core SDK](/dotnet/core/sdk)。</span><span class="sxs-lookup"><span data-stu-id="6caff-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="6caff-111">**Web Server (IIS)** サーバー ロールで構成された Windows Server。</span><span class="sxs-lookup"><span data-stu-id="6caff-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="6caff-112">IIS で Web サイトをホストするようにサーバーが構成されていない場合、<xref:host-and-deploy/iis/index#iis-configuration> 記事の「*IIS 構成*」セクションの指示に従い、その後、このチュートリアルに戻ってください。</span><span class="sxs-lookup"><span data-stu-id="6caff-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="6caff-113">**IIS 構成と Web サイトのセキュリティには、このチュートリアルでは説明されていない概念が含まれています。**</span><span class="sxs-lookup"><span data-stu-id="6caff-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="6caff-114">IIS で本稼働アプリをホストする前に、[Microsoft IIS ドキュメント](https://www.iis.net/)と [IIS でホストする方法に関する ASP.NET Core 記事](xref:host-and-deploy/iis/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6caff-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="6caff-115">このチュートリアルで取り上げられていない IIS ホスティングの重要なシナリオ:</span><span class="sxs-lookup"><span data-stu-id="6caff-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="6caff-116">ASP.NET Core データ保護のためのレジストリ ハイブの作成</span><span class="sxs-lookup"><span data-stu-id="6caff-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/index#data-protection)
> * [<span data-ttu-id="6caff-117">アプリ プールのアクセス制御リスト (ACL) の構成</span><span class="sxs-lookup"><span data-stu-id="6caff-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/index#application-pool-identity)
> * <span data-ttu-id="6caff-118">IIS 配置の概念を集中的に取り上げるために、このチュートリアルでは、IIS で HTTPS セキュリティを構成せずにアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="6caff-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="6caff-119">HTTPS プロトコルに対して有効にするアプリをホストする方法については、この記事の「[その他の技術情報](#additional-resources)」セクションのセキュリティ トピックをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6caff-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="6caff-120">ASP.NET Core アプリのホスティングに関するその他の情報は <xref:host-and-deploy/iis/index> 記事にあります。</span><span class="sxs-lookup"><span data-stu-id="6caff-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="6caff-121">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="6caff-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6caff-122">*.NET Core ホスティング バンドル*を IIS サーバーにインストールします。</span><span class="sxs-lookup"><span data-stu-id="6caff-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="6caff-123">このバンドルをインストールすることで、.NET Core ランタイム、.NET Core ライブラリ、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="6caff-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6caff-124">このモジュールでは、ASP.NET Core アプリが IIS の背後で実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6caff-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="6caff-125">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6caff-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="6caff-126">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="6caff-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="6caff-127">IIS サーバーでインストーラーを実行します。</span><span class="sxs-lookup"><span data-stu-id="6caff-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="6caff-128">サーバーを再起動するか、コマンド シェルで `net stop was /y` に続けて `net start w3svc` を実行します。</span><span class="sxs-lookup"><span data-stu-id="6caff-128">Restart the server or execute `net stop was /y` followed by `net start w3svc` in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="6caff-129">IIS サイトを作成する</span><span class="sxs-lookup"><span data-stu-id="6caff-129">Create the IIS site</span></span>

1. <span data-ttu-id="6caff-130">IIS サーバーで、アプリの公開フォルダーとファイルを格納するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="6caff-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="6caff-131">以下の手順では、フォルダーのパスはアプリへの物理パスとして IIS に提供されます。</span><span class="sxs-lookup"><span data-stu-id="6caff-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="6caff-132">アプリの配置フォルダーとファイル レイアウトについて詳しくは、<xref:host-and-deploy/directory-structure> をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6caff-132">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="6caff-133">IIS マネージャーの **[接続]** パネルで、サーバーのノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="6caff-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="6caff-134">**[サイト]** フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="6caff-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="6caff-135">コンテキスト メニューで **[Web サイトの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6caff-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="6caff-136">**[サイト名]** を指定し、 **[物理パス]** には作成したアプリの配置フォルダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="6caff-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="6caff-137">**[バインド]** の構成を指定して **[OK]** を選択し、Web サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="6caff-137">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

   > [!WARNING]
   > <span data-ttu-id="6caff-138">最上位のワイルドカードのバインド ( `http://*:80/` と `http://+:80` ) は使用しては **いけません** 。</span><span class="sxs-lookup"><span data-stu-id="6caff-138">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="6caff-139">最上位のワイルドカードのバインドは、セキュリティの脆弱性に対してアプリを切り開くことができます。</span><span class="sxs-lookup"><span data-stu-id="6caff-139">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="6caff-140">これは、強力と脆弱の両方のワイルドカードに適用されます。</span><span class="sxs-lookup"><span data-stu-id="6caff-140">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="6caff-141">ワイルドカードではなく、明示的なホスト名を使用します。</span><span class="sxs-lookup"><span data-stu-id="6caff-141">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="6caff-142">全体の親ドメインを制御する場合、サブドメイン ワイルドカード バインド (たとえば、`*.mysub.com`) にこのセキュリティ リスクはありません (脆弱である `*.com` とは対照的)。</span><span class="sxs-lookup"><span data-stu-id="6caff-142">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="6caff-143">詳細については、[rfc7230 セクション-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6caff-143">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="6caff-144">プロセス モデル ID に適切なアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6caff-144">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="6caff-145">アプリ プールの既定の ID ( **[プロセス モデル]**  >  **[Identity]** ) を `ApplicationPoolIdentity` から別の ID に変更した場合は、アプリのフォルダー、データベース、その他の必要なリソースにアクセスするために要求されるアクセス許可が新しい ID に設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="6caff-145">If the default identity of the app pool (**Process Model** > **Identity**) is changed from `ApplicationPoolIdentity` to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="6caff-146">たとえば、アプリケーション プールには、アプリがファイルの読み取りおよび書き込みを行うフォルダーへの読み取りおよび書き込みアクセスが必要です。</span><span class="sxs-lookup"><span data-stu-id="6caff-146">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="6caff-147">ASP.NET Core Razor Pages アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="6caff-147">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="6caff-148"><xref:getting-started> チュートリアルに従い、Razor Pages アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="6caff-148">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="6caff-149">アプリを発行および配置する</span><span class="sxs-lookup"><span data-stu-id="6caff-149">Publish and deploy the app</span></span>

<span data-ttu-id="6caff-150">"*アプリを発行する*" とは、サーバーでホストできるコンパイル済みのアプリを生成するということです。</span><span class="sxs-lookup"><span data-stu-id="6caff-150">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="6caff-151">"*アプリを展開する*" とは、発行済みのアプリをホスティング システムに移動するということです。</span><span class="sxs-lookup"><span data-stu-id="6caff-151">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="6caff-152">発行手順は [.NET Core SDK](/dotnet/core/sdk) で処理されます。一方で、展開手順はさまざまな手法で処理できます。</span><span class="sxs-lookup"><span data-stu-id="6caff-152">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="6caff-153">このチュートリアルでは、"*フォルダー*" 展開手法を採用しています。</span><span class="sxs-lookup"><span data-stu-id="6caff-153">This tutorial adopts the *folder* deployment approach, where:</span></span>
 
* <span data-ttu-id="6caff-154">アプリはフォルダーに発行されます。</span><span class="sxs-lookup"><span data-stu-id="6caff-154">The app is published to a folder.</span></span>
* <span data-ttu-id="6caff-155">フォルダーの内容が IIS サイトのフォルダーに移動されます (IIS マネージャーのサイトの**物理パス**)。</span><span class="sxs-lookup"><span data-stu-id="6caff-155">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6caff-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6caff-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6caff-157">**[ソリューション エクスプローラー]** でプロジェクトを右クリックし、 **[発行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="6caff-157">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="6caff-158">**[発行先を選択]** ダイアログで、 **[フォルダー]** 発行オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="6caff-158">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="6caff-159">**フォルダーまたはファイル共有**パスを選択します。</span><span class="sxs-lookup"><span data-stu-id="6caff-159">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="6caff-160">ネットワーク共有として開発用のコンピューターで利用できる IIS サイトのフォルダーを作成した場合、共有へのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="6caff-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="6caff-161">現在のユーザーに、共有に発行するための書き込みアクセスを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="6caff-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="6caff-162">IIS サーバー上の IIS サイト フォルダーに直接展開できない場合、リムーバブル メディア上のフォルダーに発行し、IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに発行済みのアプリを物理的に移動します。</span><span class="sxs-lookup"><span data-stu-id="6caff-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="6caff-163">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに `bin/Release/{TARGET FRAMEWORK}/publish` フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="6caff-163">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6caff-164">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6caff-164">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="6caff-165">コマンド シェルで、[dotnet publish](/dotnet/core/tools/dotnet-publish) コマンドを使用してリリース構成でアプリを発行します。</span><span class="sxs-lookup"><span data-stu-id="6caff-165">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="6caff-166">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに `bin/Release/{TARGET FRAMEWORK}/publish` フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="6caff-166">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6caff-167">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="6caff-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6caff-168">**[ソリューション]** でプロジェクトを右クリックし、 **[発行]**  >  **[フォルダーに発行]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="6caff-168">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="6caff-169">**[フォルダーを選択してください]** パスを設定します。</span><span class="sxs-lookup"><span data-stu-id="6caff-169">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="6caff-170">ネットワーク共有として開発用のコンピューターで利用できる IIS サイトのフォルダーを作成した場合、共有へのパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="6caff-170">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="6caff-171">現在のユーザーに、共有に発行するための書き込みアクセスを与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="6caff-171">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="6caff-172">IIS サーバー上の IIS サイト フォルダーに直接展開できない場合、リムーバブル メディア上のフォルダーに発行し、IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに発行済みのアプリを物理的に移動します。</span><span class="sxs-lookup"><span data-stu-id="6caff-172">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="6caff-173">IIS マネージャーでサイトの**物理パス**である、サーバー上の IIS サイト フォルダーに `bin/Release/{TARGET FRAMEWORK}/publish` フォルダーの内容を移動します。</span><span class="sxs-lookup"><span data-stu-id="6caff-173">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="6caff-174">Web サイトを閲覧する</span><span class="sxs-lookup"><span data-stu-id="6caff-174">Browse the website</span></span>

<span data-ttu-id="6caff-175">アプリには、最初の要求の受信後、ブラウザーでアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6caff-175">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="6caff-176">サイトの IIS マネージャーで設定したエンドポイント バインドでアプリへの要求を行います。</span><span class="sxs-lookup"><span data-stu-id="6caff-176">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6caff-177">次の手順</span><span class="sxs-lookup"><span data-stu-id="6caff-177">Next steps</span></span>

<span data-ttu-id="6caff-178">このチュートリアルでは、次の作業を行う方法を学びました。</span><span class="sxs-lookup"><span data-stu-id="6caff-178">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6caff-179">.NET Core ホスティング バンドルを Windows Server にインストールします。</span><span class="sxs-lookup"><span data-stu-id="6caff-179">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="6caff-180">IIS サイトを IIS マネージャーで作成します。</span><span class="sxs-lookup"><span data-stu-id="6caff-180">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="6caff-181">ASP.NET Core アプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="6caff-181">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="6caff-182">IIS で ASP.NET Core アプリをホストする方法の詳細については、IIS の概要に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6caff-182">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="6caff-183">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6caff-183">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="6caff-184">ASP.NET Core ドキュメント セットの記事</span><span class="sxs-lookup"><span data-stu-id="6caff-184">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="6caff-185">ASP.NET Core アプリの展開に関する記事</span><span class="sxs-lookup"><span data-stu-id="6caff-185">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="6caff-186">Visual Studio for Mac を使用してフォルダーに Web アプリを発行する</span><span class="sxs-lookup"><span data-stu-id="6caff-186">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="6caff-187">IIS HTTPS 構成に関する記事</span><span class="sxs-lookup"><span data-stu-id="6caff-187">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="6caff-188">IIS マネージャーで SSL を構成する</span><span class="sxs-lookup"><span data-stu-id="6caff-188">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="6caff-189">IIS で SSL を設定する方法</span><span class="sxs-lookup"><span data-stu-id="6caff-189">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="6caff-190">IIS と Windows Server に関する記事</span><span class="sxs-lookup"><span data-stu-id="6caff-190">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="6caff-191">Microsoft IIS 公式サイト</span><span class="sxs-lookup"><span data-stu-id="6caff-191">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="6caff-192">Windows Server テクニカル コンテンツ ライブラリ</span><span class="sxs-lookup"><span data-stu-id="6caff-192">Windows Server technical content library</span></span>](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="6caff-193">IIS 管理者用の展開リソース</span><span class="sxs-lookup"><span data-stu-id="6caff-193">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="6caff-194">IIS ドキュメント </span><span class="sxs-lookup"><span data-stu-id="6caff-194">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="6caff-195">IIS での IIS マネージャーの概要</span><span class="sxs-lookup"><span data-stu-id="6caff-195">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="6caff-196">.NET Core アプリケーションの展開</span><span class="sxs-lookup"><span data-stu-id="6caff-196">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

