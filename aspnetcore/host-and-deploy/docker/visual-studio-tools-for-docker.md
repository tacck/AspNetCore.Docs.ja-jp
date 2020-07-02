---
title: Visual Studio コンテナー ツールと ASP.NET Core
author: spboyer
description: Visual Studio ツールと Docker for Windows を使用して、ASP.NET Core アプリをコンテナー化する方法を説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 2b0b1a869f41bcaed59d9bed89f7fcdb58d0f6c1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407734"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="06e00-103">Visual Studio コンテナー ツールと ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06e00-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="06e00-104">Visual Studio 2017 以降のバージョンでは、.NET Core をターゲットとするコンテナー化された ASP.NET Core アプリのビルド、デバッグ、実行がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="06e00-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="06e00-105">Windows と Linux の両方のコンテナーがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="06e00-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="06e00-106">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="06e00-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06e00-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="06e00-107">Prerequisites</span></span>

* [<span data-ttu-id="06e00-108">Docker for Windows</span><span class="sxs-lookup"><span data-stu-id="06e00-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="06e00-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **.NET Core クロスプラットフォームの開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="06e00-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="06e00-110">インストールとセットアップ</span><span class="sxs-lookup"><span data-stu-id="06e00-110">Installation and setup</span></span>

<span data-ttu-id="06e00-111">Docker をインストールするには、まず、「[Docker for Windows:What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)」 (Docker Desktop for Windows: インストール前に知っておくべきこと) の情報を確認します。</span><span class="sxs-lookup"><span data-stu-id="06e00-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="06e00-112">次に、[Docker for Windows](https://docs.docker.com/docker-for-windows/install/) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="06e00-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="06e00-113">ボリュームのマッピングとデバッグをサポートするように、Docker for Windows で **[共有ドライブ](https://docs.docker.com/docker-for-windows/#shared-drives)**  を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="06e00-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="06e00-114">システム トレイの Docker アイコンを右クリックし、 **[設定]** 、 **[Shared Drives]\(共有ドライブ\)** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="06e00-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="06e00-115">Docker がファイルを保存するドライブを選択します。</span><span class="sxs-lookup"><span data-stu-id="06e00-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="06e00-116">**[適用]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="06e00-116">Click **Apply**.</span></span>

![コンテナーで共有するローカル C ドライブを選択するためのダイアログ](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="06e00-118">Visual Studio 2017 バージョン 15.6 以降では、**共有ドライブ**が構成されていない場合にプロンプトが表示されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="06e00-119">Docker コンテナーにプロジェクトを追加する</span><span class="sxs-lookup"><span data-stu-id="06e00-119">Add a project to a Docker container</span></span>

<span data-ttu-id="06e00-120">ASP.NET Core プロジェクトをコンテナー化するには、プロジェクトで .NET Core をターゲットにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="06e00-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="06e00-121">Linux と Windows の両方のコンテナーがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="06e00-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="06e00-122">プロジェクトに Docker サポートを追加する場合は、Windows または Linux のいずれかのコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="06e00-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="06e00-123">Docker ホストが同じコンテナーの種類を実行している必要があります。</span><span class="sxs-lookup"><span data-stu-id="06e00-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="06e00-124">実行中の Docker インスタンスでコンテナーの種類を変更するには、システム トレイの Docker アイコンを右クリックして、 **[Switch to Windows containers...]\(Windows コンテナーに切り替える...\)** または **[Switch to Linux containers...]\(Linux コンテナーに切り替える...\)** を選択します。</span><span class="sxs-lookup"><span data-stu-id="06e00-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="06e00-125">新しいアプリ</span><span class="sxs-lookup"><span data-stu-id="06e00-125">New app</span></span>

<span data-ttu-id="06e00-126">**ASP.NET Core Web アプリケーション** プロジェクト テンプレートを使用して新しいアプリを作成する場合は、次のように **[Enable Docker Support]\(Docker サポートを有効にする\)** チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="06e00-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![[Enable Docker Support]\(Docker サポートを有効にする\) チェック ボックス](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="06e00-128">ターゲット フレームワークが .NET Core の場合、 **[OS]** ドロップダウンでコンテナーの種類を選択できます。</span><span class="sxs-lookup"><span data-stu-id="06e00-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="06e00-129">既存のアプリ</span><span class="sxs-lookup"><span data-stu-id="06e00-129">Existing app</span></span>

<span data-ttu-id="06e00-130">.NET Core をターゲットとする ASP.NET Core プロジェクトの場合、ツールを使用して Docker サポートを追加するための 2 つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="06e00-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="06e00-131">Visual Studio でプロジェクトを開き、次のオプションのいずれかを選択します。</span><span class="sxs-lookup"><span data-stu-id="06e00-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="06e00-132">**[プロジェクト]** メニューから **[Docker サポート]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="06e00-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="06e00-133">**ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[追加]**  >  **[Docker サポート]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="06e00-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="06e00-134">Visual Studio コンテナー ツールでは、.NET Framework をターゲットとする既存の ASP.NET Core プロジェクトへの Docker の追加はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="06e00-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="06e00-135">Dockerfile の概要</span><span class="sxs-lookup"><span data-stu-id="06e00-135">Dockerfile overview</span></span>

<span data-ttu-id="06e00-136">*Dockerfile* (Docker の最終イメージを作成するためのレシピ) は、プロジェクトのルートに追加されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="06e00-137">その中に含まれるコマンドの詳細については、「[Dockerfile reference](https://docs.docker.com/engine/reference/builder/)」 (Dockerfile リファレンス) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="06e00-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="06e00-138">この特定の *Dockerfile* では、次のような、4 つの異なる名前付きのビルド ステージを含む、[multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) を使用します。</span><span class="sxs-lookup"><span data-stu-id="06e00-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="06e00-139">上記の *Dockerfile* は、[microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) イメージに基づいています。</span><span class="sxs-lookup"><span data-stu-id="06e00-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="06e00-140">この基本イメージには、ASP.NET Core ランタイムと NuGet パッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="06e00-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="06e00-141">パッケージは、起動時のパフォーマンスを向上させるために JIT (Just-In-Time) コンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="06e00-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="06e00-142">新しいプロジェクト ダイアログの **[Configure for HTTPS]\(HTTPS 用に構成する\)** チェック ボックスがオンになっている場合、*Dockerfile* は 2 つのポートを公開します。</span><span class="sxs-lookup"><span data-stu-id="06e00-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="06e00-143">1 つのポートは HTTP トラフィック用、もう 1 つのポートは HTTPS 用に使用されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="06e00-144">チェック ボックスがオンになっていない場合は、HTTP トラフィック用に単一のポート (80) が公開されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="06e00-145">上記の *Dockerfile* は、[microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) イメージに基づいています。</span><span class="sxs-lookup"><span data-stu-id="06e00-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="06e00-146">この基本イメージには、起動時のパフォーマンスを向上させるために JIT (Just-In-Time) コンパイルされる、ASP.NET Core NuGet パッケージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="06e00-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="06e00-147">アプリにコンテナー オーケストレーター サポートを追加する</span><span class="sxs-lookup"><span data-stu-id="06e00-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="06e00-148">Visual Studio 2017 バージョン 15.7 以前では、唯一のコンテナー オーケストレーション ソリューションとして、[Docker Compose](https://docs.docker.com/compose/overview/) がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="06e00-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="06e00-149">Docker Compose の成果物は、 **[追加]**  >  **[Docker サポート]** を使用して追加されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="06e00-150">Visual Studio 2017 バージョン 15.8 以降では、指示された場合にのみ、オーケストレーション ソリューションが追加されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="06e00-151">**ソリューション エクスプローラー**でプロジェクトを右クリックして、 **[追加]**  >  **[Container Orchestrator Support]\(コンテナー オーケストレーター サポート)** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="06e00-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="06e00-152">次の選択肢があります。</span><span class="sxs-lookup"><span data-stu-id="06e00-152">The following choices are available:</span></span> 

* [<span data-ttu-id="06e00-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="06e00-153">Docker Compose</span></span>](#docker-compose)
* [<span data-ttu-id="06e00-154">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="06e00-154">Service Fabric</span></span>](#service-fabric)
* [<span data-ttu-id="06e00-155">Kubernetes/Helm </span><span class="sxs-lookup"><span data-stu-id="06e00-155">Kubernetes/Helm </span></span>](https://helm.sh/)

### <a name="docker-compose"></a><span data-ttu-id="06e00-156">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="06e00-156">Docker Compose</span></span>

<span data-ttu-id="06e00-157">Visual Studio コンテナー ツールでは、ソリューションに *docker-compose* プロジェクトを追加します。これには以下のファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="06e00-157">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="06e00-158">*docker-compose.dcproj*:プロジェクトを表すファイル。</span><span class="sxs-lookup"><span data-stu-id="06e00-158">*docker-compose.dcproj*: The file representing the project.</span></span> <span data-ttu-id="06e00-159">使用する OS を指定する `<DockerTargetOS>` 要素が含まれます。</span><span class="sxs-lookup"><span data-stu-id="06e00-159">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="06e00-160">*.dockerignore*:ビルド コンテキストを生成するときに除外するファイルとディレクトリのパターンが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-160">*.dockerignore*: Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="06e00-161">*docker-compose.yml*:`docker-compose build` および `docker-compose run` を使用して、それぞれビルドおよび実行されるイメージのコレクションを定義するために使用される、基本の [Docker Compose](https://docs.docker.com/compose/overview/) ファイル。</span><span class="sxs-lookup"><span data-stu-id="06e00-161">*docker-compose.yml*: The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="06e00-162">*docker-compose.override.yml*:サービスの構成オーバーライドを含む、Docker Compose によって読み取られるオプション ファイル。</span><span class="sxs-lookup"><span data-stu-id="06e00-162">*docker-compose.override.yml*: An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="06e00-163">Visual Studio は `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` を実行してこれらのファイルをマージします。</span><span class="sxs-lookup"><span data-stu-id="06e00-163">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="06e00-164">*docker-compose.yml* ファイルでは、プロジェクトの実行時に作成されたイメージの名前を参照します。</span><span class="sxs-lookup"><span data-stu-id="06e00-164">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="06e00-165">上の例では、`image: hellodockertools` によって、アプリが**デバッグ** モードで実行されるときに、イメージ `hellodockertools:dev` が生成されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-165">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="06e00-166">`hellodockertools:latest` イメージは、アプリが**リリース** モードで実行されるときに生成されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-166">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="06e00-167">イメージがレジストリにプッシュされる場合は、イメージ名の前に [Docker Hub](https://hub.docker.com/) のユーザー名を付けます (例: `dockerhubusername/hellodockertools`)。</span><span class="sxs-lookup"><span data-stu-id="06e00-167">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="06e00-168">または、構成に応じて、プライベート レジストリ URL を含めるようにイメージ名を変更します (例: `privateregistry.domain.com/hellodockertools`)。</span><span class="sxs-lookup"><span data-stu-id="06e00-168">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="06e00-169">ビルド構成に基づいて動作を別にしたい場合 (デバッグやリリースなど)、構成固有の*docker-compose* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="06e00-169">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="06e00-170">ビルド構成に基づいて (たとえば、*docker-compose.vs.debug.yml* および *docker-compose.vs.release.yml*) ファイル名を指定して、*docker-compose-override.yml* ファイルと同じ場所にそのファイルを配置する必要があります。</span><span class="sxs-lookup"><span data-stu-id="06e00-170">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="06e00-171">構成固有の上書きファイルを使用して、デバッグおよびリリース ビルド構成に対して、さまざまな構成設定 (環境変数やエンドポイントなど) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="06e00-171">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="06e00-172">Docker Compose で Visual Studio で実行するオプションを表示するには、Docker プロジェクトがスタートアップ プロジェクトである必要があります。</span><span class="sxs-lookup"><span data-stu-id="06e00-172">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="06e00-173">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="06e00-173">Service Fabric</span></span>

<span data-ttu-id="06e00-174">基本的な[前提条件](#prerequisites)に加え、[Service Fabric](/azure/service-fabric/) オーケストレーション ソリューションでは次の前提条件が求められます。</span><span class="sxs-lookup"><span data-stu-id="06e00-174">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="06e00-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) バージョン 2.6 以降</span><span class="sxs-lookup"><span data-stu-id="06e00-175">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="06e00-176">Visual Studio の **Azure Development** ワークロード</span><span class="sxs-lookup"><span data-stu-id="06e00-176">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="06e00-177">Service Fabric では、Windows 上のローカル開発クラスターでの Linux コンテナーの実行はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="06e00-177">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="06e00-178">プロジェクトで既に Linux コンテナーが使用されている場合は、Visual Studio で Windows コンテナーに切り替えるよう求められます。</span><span class="sxs-lookup"><span data-stu-id="06e00-178">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="06e00-179">Visual Studio コンテナーツールでは、次のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="06e00-179">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="06e00-180">ソリューションに、 *&lt;プロジェクト名&gt;Application* **Service Fabric アプリケーション** プロジェクトが追加されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-180">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="06e00-181">*Dockerfile* と *.dockerignore* ファイルを ASP.NET Core プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="06e00-181">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="06e00-182">*Dockerfile* が既に ASP.NET Core プロジェクトに存在する場合は、名前が *Dockerfile.original* に変更されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-182">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="06e00-183">次のような、新しい *Dockerfile* が作成されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-183">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="06e00-184">`<IsServiceFabricServiceProject>` 要素を ASP.NET Core プロジェクトの *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="06e00-184">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="06e00-185">*PackageRoot* フォルダーを ASP.NET Core プロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="06e00-185">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="06e00-186">フォルダーには、新しいサービスのサービス マニフェストと設定が含まれます。</span><span class="sxs-lookup"><span data-stu-id="06e00-186">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="06e00-187">詳細については、「[Windows コンテナー内の .NET アプリケーションを Azure Service Fabric にデプロイする](/azure/service-fabric/service-fabric-host-app-in-a-container)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="06e00-187">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="06e00-188">デバッグ</span><span class="sxs-lookup"><span data-stu-id="06e00-188">Debug</span></span>

<span data-ttu-id="06e00-189">ツールバーのデバッグ ドロップダウンから **[Docker]** を選択し、アプリのデバッグを開始します。</span><span class="sxs-lookup"><span data-stu-id="06e00-189">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="06e00-190">**[出力]** ウィンドウの **[Docker]** ビューには、行われている次のアクションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-190">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="06e00-191">*microsoft/dotnet* ランタイム イメージの *2.1-aspnetcore-runtime* タグが取得されます (キャッシュにまだ存在しない場合)。</span><span class="sxs-lookup"><span data-stu-id="06e00-191">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="06e00-192">イメージでは、ASP.NET Core と .NET Core ランタイムおよび関連付けられているライブラリがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="06e00-192">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="06e00-193">運用環境で ASP.NET Core アプリを実行するために最適化されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-193">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="06e00-194">`ASPNETCORE_ENVIRONMENT` 環境変数は、コンテナー内で `Development` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-194">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="06e00-195">HTTP と HTTPS 用に 1 つずつ、2 つの動的に割り当てられたポートが公開されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-195">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="06e00-196">localhost に割り当てられたポートは、`docker ps` コマンドを使用してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="06e00-196">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="06e00-197">アプリがコンテナーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="06e00-197">The app is copied to the container.</span></span>
* <span data-ttu-id="06e00-198">動的に割り当てられたポートを使用して、デバッガーがコンテナーにアタッチされ、既定のブラウザーが起動します。</span><span class="sxs-lookup"><span data-stu-id="06e00-198">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="06e00-199">アプリの結果の Docker イメージは、*dev* としてタグ付けされます。</span><span class="sxs-lookup"><span data-stu-id="06e00-199">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="06e00-200">イメージは、*microsoft/dotnet* 基本イメージの *2.1-aspnetcore-runtime* タグに基づいています。</span><span class="sxs-lookup"><span data-stu-id="06e00-200">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="06e00-201">**パッケージ マネージャー コンソール** (PMC) ウィンドウで `docker images` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="06e00-201">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="06e00-202">コンピューター上のイメージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-202">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="06e00-203">*microsoft/aspnetcore* ランタイム イメージが取得されます (まだキャッシュにない場合)。</span><span class="sxs-lookup"><span data-stu-id="06e00-203">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="06e00-204">`ASPNETCORE_ENVIRONMENT` 環境変数は、コンテナー内で `Development` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-204">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="06e00-205">ポート 80 が公開され、localhost 用に動的に割り当てられているポートにマップされます。</span><span class="sxs-lookup"><span data-stu-id="06e00-205">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="06e00-206">このポートは、Docker ホストによって決定され、`docker ps` コマンドでクエリを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="06e00-206">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="06e00-207">アプリがコンテナーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="06e00-207">The app is copied to the container.</span></span>
* <span data-ttu-id="06e00-208">動的に割り当てられたポートを使用して、デバッガーがコンテナーにアタッチされ、既定のブラウザーが起動します。</span><span class="sxs-lookup"><span data-stu-id="06e00-208">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="06e00-209">アプリの結果の Docker イメージは、*dev* としてタグ付けされます。</span><span class="sxs-lookup"><span data-stu-id="06e00-209">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="06e00-210">イメージは、*microsoft/aspnetcore* 基本イメージに基づいています。</span><span class="sxs-lookup"><span data-stu-id="06e00-210">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="06e00-211">**パッケージ マネージャー コンソール** (PMC) ウィンドウで `docker images` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="06e00-211">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="06e00-212">コンピューター上のイメージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-212">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="06e00-213">**デバッグ**構成では、反復にボリューム マウントを使用するため、*dev*イメージにはアプリのコンテンツはありません。</span><span class="sxs-lookup"><span data-stu-id="06e00-213">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="06e00-214">イメージをプッシュするには、**リリース**構成を使用します。</span><span class="sxs-lookup"><span data-stu-id="06e00-214">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="06e00-215">PMC で `docker ps` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="06e00-215">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="06e00-216">アプリがコンテナーを使用して実行されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="06e00-216">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="06e00-217">エディット コンティニュ</span><span class="sxs-lookup"><span data-stu-id="06e00-217">Edit and continue</span></span>

<span data-ttu-id="06e00-218">静的なファイルや Razor ビューに対する変更は、コンパイルの手順を行う必要はなく、自動的に更新されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-218">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="06e00-219">変更を行って保存し、ブラウザーを更新し、更新を確認します。</span><span class="sxs-lookup"><span data-stu-id="06e00-219">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="06e00-220">コード ファイルを変更する場合、コンテナー内で Kestrel をコンパイルして再起動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="06e00-220">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="06e00-221">変更したら、`CTRL+F5` キーを使用して、コンテナー内でプロセスを実行し、アプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="06e00-221">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="06e00-222">Docker コンテナーが再構築されたり、停止されたりすることはありません。</span><span class="sxs-lookup"><span data-stu-id="06e00-222">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="06e00-223">PMC で `docker ps` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="06e00-223">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="06e00-224">元のコンテナーが 10 分前と同じ状態で実行されていることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="06e00-224">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="06e00-225">Docker イメージの発行</span><span class="sxs-lookup"><span data-stu-id="06e00-225">Publish Docker images</span></span>

<span data-ttu-id="06e00-226">アプリの開発とデバッグのサイクルが完了したら、Visual Studio コンテナーツールでアプリの実稼働イメージを作成できます。</span><span class="sxs-lookup"><span data-stu-id="06e00-226">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="06e00-227">構成ドロップダウンを **[リリース]** に変更し、アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="06e00-227">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="06e00-228">ツールは、Docker Hub からコンパイル/発行イメージを取得します (まだキャッシュに存在しない場合)。</span><span class="sxs-lookup"><span data-stu-id="06e00-228">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="06e00-229">イメージは、プライベート レジストリまたは Docker Hub にプッシュできる *latest* タグ付きで生成されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-229">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="06e00-230">PMC で `docker images` コマンドを実行して、イメージの一覧を表示します。</span><span class="sxs-lookup"><span data-stu-id="06e00-230">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="06e00-231">次のような出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-231">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="06e00-232">上記の出力に一覧表示されている `microsoft/aspnetcore-build` および `microsoft/aspnetcore` イメージは、.NET Core 2.1 の時点で `microsoft/dotnet` イメージに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="06e00-232">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="06e00-233">詳細については、[Docker リポジトリの移行に関するお知らせ](https://github.com/aspnet/Announcements/issues/298)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="06e00-233">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="06e00-234">`docker images` コマンドは、 *\<none>* として識別されるリポジトリ名とタグを持つ中間イメージを返します (上にはリストされていません)。</span><span class="sxs-lookup"><span data-stu-id="06e00-234">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="06e00-235">これらの名前のないイメージは、[multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile* によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-235">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="06e00-236">これにより、最終イメージの構築効率が向上します&mdash;変更時には必要なレイヤーのみが再構築されます。</span><span class="sxs-lookup"><span data-stu-id="06e00-236">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="06e00-237">中間イメージが不要になった場合は、[docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) コマンドを使用して削除します。</span><span class="sxs-lookup"><span data-stu-id="06e00-237">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="06e00-238">*dev* イメージと比較した場合、実稼働またはリリース イメージはサイズが小さいと思うかもしれません。</span><span class="sxs-lookup"><span data-stu-id="06e00-238">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="06e00-239">ボリューム マッピングにより、デバッガーとアプリは、コンテナー内ではなく、ローカル コンピューターから実行されています。</span><span class="sxs-lookup"><span data-stu-id="06e00-239">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="06e00-240">*latest* イメージには、ホスト コンピューターでアプリを実行するために必要なアプリ コードがパッケージ化されています。</span><span class="sxs-lookup"><span data-stu-id="06e00-240">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="06e00-241">そのため、デルタはアプリ コードのサイズです。</span><span class="sxs-lookup"><span data-stu-id="06e00-241">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="06e00-242">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="06e00-242">Additional resources</span></span>

* [<span data-ttu-id="06e00-243">Visual Studio によるコンテナー開発</span><span class="sxs-lookup"><span data-stu-id="06e00-243">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="06e00-244">Azure Service Fabric:開発環境を準備する</span><span class="sxs-lookup"><span data-stu-id="06e00-244">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="06e00-245">Windows コンテナー内の .NET アプリケーションを Azure Service Fabric にデプロイする</span><span class="sxs-lookup"><span data-stu-id="06e00-245">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="06e00-246">Docker を使用した Visual Studio 開発のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="06e00-246">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="06e00-247">Visual Studio コンテナー ツールの GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="06e00-247">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
* [<span data-ttu-id="06e00-248">ドッカーと小さなコンテナを使用した GC</span><span class="sxs-lookup"><span data-stu-id="06e00-248">GC using Docker and small containers</span></span>](xref:performance/memory#sc)
