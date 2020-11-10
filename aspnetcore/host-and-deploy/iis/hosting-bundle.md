---
title: ホスティング バンドル
author: rick-anderson
description: .NET Core ホスティング バンドルを構成する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343638"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="6e34b-103">.NET Core ホスティング バンドル</span><span class="sxs-lookup"><span data-stu-id="6e34b-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6e34b-104">.NET Core ホスティング バンドルは、.NET Core ランタイムおよび [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)用のインストーラーです。</span><span class="sxs-lookup"><span data-stu-id="6e34b-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="6e34b-105">バンドルを使用すると、ASP.NET Core アプリを IIS で実行できます。</span><span class="sxs-lookup"><span data-stu-id="6e34b-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="6e34b-106">.NET Core ホスティング バンドルのインストール</span><span class="sxs-lookup"><span data-stu-id="6e34b-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6e34b-107">ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e34b-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="6e34b-108">IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="6e34b-109">.NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。</span><span class="sxs-lookup"><span data-stu-id="6e34b-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="6e34b-110">この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e34b-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="6e34b-111">直接ダウンロード (現在のバージョン)</span><span class="sxs-lookup"><span data-stu-id="6e34b-111">Direct download (current version)</span></span>

<span data-ttu-id="6e34b-112">次のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="6e34b-113">現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)</span><span class="sxs-lookup"><span data-stu-id="6e34b-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="6e34b-114">Visual C++ 再頒布可能パッケージの要件</span><span class="sxs-lookup"><span data-stu-id="6e34b-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="6e34b-115">Windows Server 2012 R2 など、古いバージョンの Windows では、Visual Studio C++ 2015、2017、2019 の再頒布可能パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="6e34b-116">そうしないと、`The data is the error.` という混乱を招くエラー メッセージが Windows イベント ログで報告されます。</span><span class="sxs-lookup"><span data-stu-id="6e34b-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="6e34b-117">[現在の x64 VS C++ 再頒布可能パッケージ](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[現在の x86 VS C++ 再頒布可能パッケージ](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="6e34b-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="6e34b-118">以前のバージョンのインストーラー</span><span class="sxs-lookup"><span data-stu-id="6e34b-118">Earlier versions of the installer</span></span>

<span data-ttu-id="6e34b-119">以前のバージョンのインストーラーを入手するには:</span><span class="sxs-lookup"><span data-stu-id="6e34b-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="6e34b-120">「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="6e34b-121">目的の .NET Core のバージョンを選択します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="6e34b-122">**[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="6e34b-123">**[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="6e34b-124">一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="6e34b-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="6e34b-125">詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6e34b-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="6e34b-126">オプション</span><span class="sxs-lookup"><span data-stu-id="6e34b-126">Options</span></span>

1. <span data-ttu-id="6e34b-127">管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6e34b-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="6e34b-128">`OPT_NO_ANCM=1`:ASP.NET Core モジュールのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="6e34b-129">`OPT_NO_RUNTIME=1`:.NET Core ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="6e34b-130">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="6e34b-131">`OPT_NO_SHAREDFX=1`:ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="6e34b-132">サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="6e34b-133">`OPT_NO_X86=1`:x86 ランタイムのインストールをスキップします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="6e34b-134">32 ビット アプリをホストしない場合は、このパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="6e34b-135">今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="6e34b-136">`OPT_NO_SHARED_CONFIG_CHECK=1`:共有構成 (`applicationHost.config`) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使用するためのチェックを無効にします。</span><span class="sxs-lookup"><span data-stu-id="6e34b-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="6e34b-137">" *ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* "</span><span class="sxs-lookup"><span data-stu-id="6e34b-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="6e34b-138">詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e34b-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="6e34b-139">IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e34b-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="6e34b-140">IIS を再開する</span><span class="sxs-lookup"><span data-stu-id="6e34b-140">Restart IIS</span></span>

<span data-ttu-id="6e34b-141">ホスティング バンドルをインストールした後は、手動で IIS を再起動することが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e34b-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="6e34b-142">たとえば、IIS ワーカー プロセスを実行するための PATH に、`dotnet` CLI ツール (コマンド) が存在しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e34b-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="6e34b-143">IIS を手動で停止して開始するには、管理者特権のコマンド シェルで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="6e34b-144">モジュールのバージョンとホスティング バンドル インストーラーのログ</span><span class="sxs-lookup"><span data-stu-id="6e34b-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="6e34b-145">インストールされている ASP.NET Core モジュールのバージョンを確認するには:</span><span class="sxs-lookup"><span data-stu-id="6e34b-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="6e34b-146">ホスティング システム上で、 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` に移動します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="6e34b-147">`aspnetcorev2.dll` ファイルを見つけます。</span><span class="sxs-lookup"><span data-stu-id="6e34b-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="6e34b-148">ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="6e34b-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="6e34b-149">**[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="6e34b-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="6e34b-150">モジュールに関するホスティング バンドル インストーラーのログは、`C:\Users\%UserName%\AppData\Local\Temp` にあります。</span><span class="sxs-lookup"><span data-stu-id="6e34b-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="6e34b-151">このファイルには `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` という名前が付けられます。ここで、プレースホルダー `{TIMESTAMP}` はファイルのタイムスタンプです。</span><span class="sxs-lookup"><span data-stu-id="6e34b-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
