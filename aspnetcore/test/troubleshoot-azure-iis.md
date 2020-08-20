---
title: Azure App Service および IIS での ASP.NET Core のトラブルシューティング
author: rick-anderson
description: ASP.NET Core アプリの Azure App Service およびインターネット インフォメーション サービス (IIS) のデプロイに関する問題を診断する方法について学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 117c777dc9ae1b8c6448f097132454b714a1b5dc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632162"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="c9fe1-103">Azure App Service および IIS での ASP.NET Core のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="c9fe1-104">作成者: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9fe1-105">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="c9fe1-106">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="c9fe1-107">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="c9fe1-108">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="c9fe1-109">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="c9fe1-110">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="c9fe1-111">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="c9fe1-112">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="c9fe1-113">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="c9fe1-114">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="c9fe1-115">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c9fe1-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="c9fe1-116">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="c9fe1-117">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-117">App startup errors</span></span>

<span data-ttu-id="c9fe1-118">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="c9fe1-119">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 - 処理エラー*" または "*500.30 - 開始エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="c9fe1-120">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="c9fe1-120">403.14 Forbidden</span></span>

<span data-ttu-id="c9fe1-121">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-121">The app fails to start.</span></span> <span data-ttu-id="c9fe1-122">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="c9fe1-123">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="c9fe1-124">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="c9fe1-125">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="c9fe1-126">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="c9fe1-127">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-127">Perform the following steps:</span></span>

1. <span data-ttu-id="c9fe1-128">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9fe1-129">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="c9fe1-130">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="c9fe1-131">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="c9fe1-132">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="c9fe1-133">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="c9fe1-134">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="c9fe1-135">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="c9fe1-136">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-136">500 Internal Server Error</span></span>

<span data-ttu-id="c9fe1-137">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="c9fe1-138">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="c9fe1-139">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="c9fe1-140">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="c9fe1-141">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="c9fe1-142">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="c9fe1-143">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="c9fe1-144">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="c9fe1-145">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-145">The worker process fails.</span></span> <span data-ttu-id="c9fe1-146">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-146">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-147">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module) コンポーネントの読み込み中に不明なエラーが発生しました。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="c9fe1-148">次のいずれかのアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-148">Take one of the following actions:</span></span>

* <span data-ttu-id="c9fe1-149">[Microsoft サポート](https://support.microsoft.com/oas/default.aspx?prid=15832)に問い合わせます ( **[開発者ツール]** 、 **[ASP.NET Core]** の順に選択します)。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="c9fe1-150">Stack Overflow について質問します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="c9fe1-151">[GitHub リポジトリ](https://github.com/dotnet/AspNetCore)で問題を報告します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="c9fe1-152">500.30 インプロセス起動エラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="c9fe1-153">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-153">The worker process fails.</span></span> <span data-ttu-id="c9fe1-154">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-154">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-155">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .NET Core CLR の開始をインプロセスで試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="c9fe1-156">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="c9fe1-157">一般的なエラー条件:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-157">Common failure conditions:</span></span>

* <span data-ttu-id="c9fe1-158">存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="c9fe1-159">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="c9fe1-160">Azure Key Vault を使用している場合、Key Vault へのアクセス許可がありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="c9fe1-161">対象の Key Vault のアクセス ポリシーを確認して、正しいアクセス許可が付与されていることを確実にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="c9fe1-162">500.31 ANCM Failed to Find Native Dependencies (500.31 ANCM ネイティブの依存関係を見つけられませんでした)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="c9fe1-163">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-163">The worker process fails.</span></span> <span data-ttu-id="c9fe1-164">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-164">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-165">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)で .NET Core ランタイムの開始がインプロセスで試行されますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="c9fe1-166">このスタートアップ エラーの最も一般的な原因は、`Microsoft.NETCore.App` または `Microsoft.AspNetCore.App` ランタイムがインストールされていない場合です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="c9fe1-167">アプリが ASP.NET Core 3.0 をターゲットとして展開されていて、そのバージョンがコンピューターに存在しない場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="c9fe1-168">エラー メッセージの例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="c9fe1-169">エラー メッセージには、インストールされているすべての .NET Core のバージョンとアプリに必要なバージョンが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="c9fe1-170">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-170">To fix this error, either:</span></span>

* <span data-ttu-id="c9fe1-171">適切なバージョンの .NET Core をマシンにインストールします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="c9fe1-172">マシンに存在する .NET Core のバージョンをターゲットにするようにアプリを変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="c9fe1-173">アプリを[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="c9fe1-174">開発環境で実行している場合 (`ASPNETCORE_ENVIRONMENT` 環境変数が `Development` に設定されている場合)、特定のエラーが HTTP 応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="c9fe1-175">プロセスのスタートアップ エラーの原因は、アプリケーション イベント ログでも見つかります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="c9fe1-176">500.32 ANCM Failed to Load dll (500.32 ANCM DLL を読み込めませんでした)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="c9fe1-177">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-177">The worker process fails.</span></span> <span data-ttu-id="c9fe1-178">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-178">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-179">このエラーの最も一般的な原因は、アプリが互換性のないプロセッサ アーキテクチャ用に発行されていることです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="c9fe1-180">ワーカー プロセスが 32 ビットアプリとして実行されていて、そのアプリが 64 ビットをターゲットとして発行されている場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="c9fe1-181">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-181">To fix this error, either:</span></span>

* <span data-ttu-id="c9fe1-182">ワーカー プロセスと同じプロセッサ アーキテクチャ用にアプリを再発行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="c9fe1-183">アプリを[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-executables-fde)として発行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="c9fe1-184">500.33 ANCM Request Handler Load Failure (500.33 ANCM 要求ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="c9fe1-185">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-185">The worker process fails.</span></span> <span data-ttu-id="c9fe1-186">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-186">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-187">アプリは `Microsoft.AspNetCore.App` フレームワークを参照していませんでした。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="c9fe1-188">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)でホストできるのは、`Microsoft.AspNetCore.App` フレームワークをターゲットとしているアプリのみです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="c9fe1-189">このエラーを修正するには、アプリが `Microsoft.AspNetCore.App` フレームワークをターゲットにしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="c9fe1-190">アプリがターゲットとしているフレームワークを確認するには、`.runtimeconfig.json` を確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="c9fe1-191">500.34 ANCM Mixed Hosting Models Not Supported (500.34 ANCM 混在ホスティング モデルはサポートされません)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="c9fe1-192">ワーカー プロセスでは、インプロセス アプリとアウト プロセス アプリの両方を同じプロセスで実行できません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="c9fe1-193">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="c9fe1-194">500.35 ANCM Multiple In-Process Applications in same Process (500.35 ANCM 同一プロセス内の複数のインプロセス アプリケーション)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="c9fe1-195">ワーカー プロセスによって、同じプロセスで複数のインプロセス アプリを実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="c9fe1-196">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="c9fe1-197">500.36 ANCM Out-Of-Process Handler Load Failure (500.36 ANCM アウト プロセス ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="c9fe1-198">アウト プロセス要求ハンドラーの *aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* ファイルの次にありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="c9fe1-199">これは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)のインストールが破損していることを示しています。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="c9fe1-200">このエラーを修正するには、[.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS 用) または Visual Studio (IIS Express 用) のインストールを修復します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="c9fe1-201">500.37 ANCM Failed to Start Within Startup Time Limit (500.37 ANCM スタートアップ時間の制限内に起動できませんでした)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="c9fe1-202">指定されたスタートアップ時間の制限内に ANCM が起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="c9fe1-203">既定では、タイムアウトは 120 秒です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="c9fe1-204">このエラーは、同じマシン上で多数のアプリを起動したときに発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="c9fe1-205">スタートアップ中のサーバー上の CPU/メモリ使用量の急上昇を確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="c9fe1-206">必要に応じて、複数のアプリのスタートアップ プロセスをずらします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="c9fe1-207">500.38 ANCM アプリケーション DLL が見つかりません</span><span class="sxs-lookup"><span data-stu-id="c9fe1-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="c9fe1-208">ANCM は、実行可能ファイルの横にあるアプリケーション DLL を見つけることができませんでした。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="c9fe1-209">このエラーは、インプロセス ホスティング モデルを使用して[単一ファイルの実行可能ファイル](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables)としてパッケージ化されたアプリをホストするときに発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="c9fe1-210">インプロセス モデルでは、ANCM によって既存の IIS プロセスに .NET Core アプリが読み込まれることが必要です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="c9fe1-211">このシナリオは、単一ファイル展開モデルではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="c9fe1-212">このエラーを修正するには、アプリのプロジェクト ファイルで、次のうち **1 つ**を使用します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="c9fe1-213">`PublishSingleFile` MSBuild プロパティを `false` に設定して、単一ファイルの公開を無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="c9fe1-214">`AspNetCoreHostingModel` MSBuild プロパティを `OutOfProcess` に設定して、アウトプロセス ホスティング モデルに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="c9fe1-215">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-215">502.5 Process Failure</span></span>

<span data-ttu-id="c9fe1-216">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-216">The worker process fails.</span></span> <span data-ttu-id="c9fe1-217">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-217">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-218">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="c9fe1-219">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="c9fe1-220">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="c9fe1-221">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="c9fe1-222">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="c9fe1-223">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="c9fe1-224">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="c9fe1-225">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="c9fe1-226">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="c9fe1-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="c9fe1-227">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="c9fe1-228">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="c9fe1-229">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="c9fe1-230">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="c9fe1-231">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="c9fe1-232">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="c9fe1-233">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="c9fe1-234">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="c9fe1-235">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="c9fe1-236">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="c9fe1-236">Connection reset</span></span>

<span data-ttu-id="c9fe1-237">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="c9fe1-238">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="c9fe1-239">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="c9fe1-240">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="c9fe1-241">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="c9fe1-241">Default startup limits</span></span>

<span data-ttu-id="c9fe1-242">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="c9fe1-243">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="c9fe1-244">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="c9fe1-245">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="c9fe1-246">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-247">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="c9fe1-248">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="c9fe1-249">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="c9fe1-250">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="c9fe1-251">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="c9fe1-252">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="c9fe1-253">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="c9fe1-254">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-255">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-256">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-257">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-258">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9fe1-259">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="c9fe1-260">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-260">Examine the log.</span></span> <span data-ttu-id="c9fe1-261">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="c9fe1-262">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="c9fe1-263">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-264">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="c9fe1-265">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-266">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-267">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-268">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="c9fe1-269">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="c9fe1-270">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="c9fe1-271">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-271">Run the app:</span></span>
   * <span data-ttu-id="c9fe1-272">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="c9fe1-273">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="c9fe1-274">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9fe1-275">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9fe1-276">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="c9fe1-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9fe1-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9fe1-278">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9fe1-279">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="c9fe1-280">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="c9fe1-281">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-281">**Current release**</span></span>

* <span data-ttu-id="c9fe1-282">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="c9fe1-283">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="c9fe1-284">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="c9fe1-285">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="c9fe1-286">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9fe1-287">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9fe1-288">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="c9fe1-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9fe1-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9fe1-290">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9fe1-291">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="c9fe1-292">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-293">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-293">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-294">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-294">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="c9fe1-295">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-295">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9fe1-296">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-296">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-297">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-297">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="c9fe1-298">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-298">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-299">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-299">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9fe1-300">Azure portal で Web アプリに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-300">In the Azure Portal, navigate to the web app.</span></span>
1. <span data-ttu-id="c9fe1-301">**[App Service]** ブレードで、検索ボックスに「**kudu**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-301">In the **App Service** blade, enter **kudu** in the search box.</span></span>
1. <span data-ttu-id="c9fe1-302">**[高度なツール]** 、 **[Go]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-302">Select **Advanced Tools** > **Go**.</span></span>
1. <span data-ttu-id="c9fe1-303">**[デバッグ コンソール]、[CMD]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-303">Select  **Debug console > CMD**.</span></span>
1. <span data-ttu-id="c9fe1-304">*site/wwwroot* に移動します</span><span class="sxs-lookup"><span data-stu-id="c9fe1-304">Navigate to *site/wwwroot*</span></span>
1. <span data-ttu-id="c9fe1-305">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-305">Select the pencil icon to edit the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-306">`<aspNetCore />` 要素で `stdoutLogEnabled="true"` を設定し、 **[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-306">In the `<aspNetCore />` element, set `stdoutLogEnabled="true"` and select **Save**.</span></span>

<span data-ttu-id="c9fe1-307">トラブルシューティングが完了したら、`stdoutLogEnabled="false"` を設定して stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-307">Disable stdout logging when troubleshooting is complete by setting `stdoutLogEnabled="false"`.</span></span>

<span data-ttu-id="c9fe1-308">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-308">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="c9fe1-309">ASP.NET Core モジュールのデバッグ ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-309">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-310">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-310">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="c9fe1-311">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-311">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9fe1-312">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-312">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="c9fe1-313">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-313">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="c9fe1-314">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-314">Redeploy the app.</span></span>
   * <span data-ttu-id="c9fe1-315">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-315">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="c9fe1-316">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-316">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-317">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-317">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-318">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-318">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="c9fe1-319">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-319">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="c9fe1-320">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-320">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9fe1-321">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-321">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="c9fe1-322">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-322">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="c9fe1-323">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-323">Select the **Save** button.</span></span>
1. <span data-ttu-id="c9fe1-324">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-324">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-325">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-325">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-326">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-326">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-327">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-327">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-328">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-328">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9fe1-329">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-329">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="c9fe1-330">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-330">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="c9fe1-331">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-331">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="c9fe1-332">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-332">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="c9fe1-333">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-333">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="c9fe1-334">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-334">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="c9fe1-335">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-335">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="c9fe1-336">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-336">Save the file.</span></span>

<span data-ttu-id="c9fe1-337">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-337">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-338">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-338">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-339">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-339">There's no limit on log file size.</span></span> <span data-ttu-id="c9fe1-340">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-340">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9fe1-341">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-341">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-342">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-342">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="c9fe1-343">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-343">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-344">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-344">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="c9fe1-345">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-345">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9fe1-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-346">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="c9fe1-347">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="c9fe1-347">Monitoring blades</span></span>

<span data-ttu-id="c9fe1-348">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-348">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="c9fe1-349">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-349">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="c9fe1-350">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-350">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="c9fe1-351">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-351">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="c9fe1-352">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-352">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="c9fe1-353">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-353">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="c9fe1-354">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-354">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="c9fe1-355">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-355">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="c9fe1-356">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-356">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c9fe1-357">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-357">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="c9fe1-358">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-358">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="c9fe1-359">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-359">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="c9fe1-360">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-360">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9fe1-361">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-361">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-362">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-362">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-363">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-363">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-364">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-364">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9fe1-365">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-365">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-366">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-366">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9fe1-367">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-367">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="c9fe1-368">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-368">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="c9fe1-369">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-369">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="c9fe1-370">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-370">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="c9fe1-371">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-371">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="c9fe1-372">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-372">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="c9fe1-373">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-373">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="c9fe1-374">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-374">Make a request to the app.</span></span>
1. <span data-ttu-id="c9fe1-375">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-375">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="c9fe1-376">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-376">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="c9fe1-377">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-377">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="c9fe1-378">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-378">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9fe1-379">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-379">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="c9fe1-380">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-380">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="c9fe1-381">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-381">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-382">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-382">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-383">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-383">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9fe1-384">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-384">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-385">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-385">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="c9fe1-386">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-386">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="c9fe1-387">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-387">Application Event Log (IIS)</span></span>

<span data-ttu-id="c9fe1-388">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-388">Access the Application Event Log:</span></span>

1. <span data-ttu-id="c9fe1-389">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-389">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c9fe1-390">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-390">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c9fe1-391">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-391">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c9fe1-392">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-392">Search for errors associated with the failing app.</span></span> <span data-ttu-id="c9fe1-393">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-393">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c9fe1-394">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-394">Run the app at a command prompt</span></span>

<span data-ttu-id="c9fe1-395">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-395">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-396">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-396">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="c9fe1-397">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="c9fe1-397">Framework-dependent deployment</span></span>

<span data-ttu-id="c9fe1-398">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-398">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="c9fe1-399">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-399">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="c9fe1-400">次のコマンドで、\<assembly_name> をアプリのアセンブリの名前に置き換えます。`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-400">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="c9fe1-401">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-401">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9fe1-402">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-402">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9fe1-403">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-403">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9fe1-404">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-404">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="c9fe1-405">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="c9fe1-405">Self-contained deployment</span></span>

<span data-ttu-id="c9fe1-406">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-406">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="c9fe1-407">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-407">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="c9fe1-408">次のコマンドで、\<assembly_name> をアプリのアセンブリの名前に置き換えます。`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-408">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="c9fe1-409">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-409">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9fe1-410">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-410">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9fe1-411">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-411">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9fe1-412">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-412">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="c9fe1-413">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-413">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="c9fe1-414">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-414">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9fe1-415">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-415">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9fe1-416">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-416">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="c9fe1-417">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-417">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="c9fe1-418">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-418">Edit the *web.config* file.</span></span> <span data-ttu-id="c9fe1-419">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-419">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="c9fe1-420">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-420">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="c9fe1-421">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-421">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="c9fe1-422">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-422">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="c9fe1-423">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-423">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="c9fe1-424">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-424">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-425">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-425">Make a request to the app.</span></span>
1. <span data-ttu-id="c9fe1-426">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-426">Navigate to the *logs* folder.</span></span> <span data-ttu-id="c9fe1-427">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-427">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="c9fe1-428">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-428">Study the log for errors.</span></span>

<span data-ttu-id="c9fe1-429">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-429">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9fe1-430">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-430">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-431">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-431">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9fe1-432">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-432">Save the file.</span></span>

<span data-ttu-id="c9fe1-433">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-433">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-434">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-434">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-435">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-435">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9fe1-436">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-436">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-437">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-437">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="c9fe1-438">ASP.NET Core モジュールのデバッグ ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-438">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="c9fe1-439">ASP.NET Core モジュールのデバッグ ログを有効にするには、次のハンドラー設定をアプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-439">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="c9fe1-440">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-440">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="c9fe1-441">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-441">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="c9fe1-442">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-442">Enable the Developer Exception Page</span></span>

<span data-ttu-id="c9fe1-443">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-443">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="c9fe1-444">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-444">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="c9fe1-445">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-445">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="c9fe1-446">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-446">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="c9fe1-447">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-447">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="c9fe1-448">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-448">Obtain data from an app</span></span>

<span data-ttu-id="c9fe1-449">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-449">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="c9fe1-450">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-450">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="c9fe1-451">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-451">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="c9fe1-452">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-452">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c9fe1-453">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="c9fe1-453">App crashes or encounters an exception</span></span>

<span data-ttu-id="c9fe1-454">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-454">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c9fe1-455">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-455">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="c9fe1-456">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-456">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="c9fe1-457">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-457">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="c9fe1-458">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-458">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="c9fe1-459">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-459">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="c9fe1-460">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-460">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c9fe1-461">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-461">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="c9fe1-462">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-462">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="c9fe1-463">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-463">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="c9fe1-464">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-464">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c9fe1-465">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-465">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-466">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-466">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c9fe1-467">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-467">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c9fe1-468">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-468">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c9fe1-469">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-469">Analyze the dump</span></span>

<span data-ttu-id="c9fe1-470">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-470">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c9fe1-471">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-471">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="c9fe1-472">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-472">Clear package caches</span></span>

<span data-ttu-id="c9fe1-473">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-473">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c9fe1-474">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-474">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c9fe1-475">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-475">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c9fe1-476">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-476">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c9fe1-477">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-477">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c9fe1-478">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-478">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c9fe1-479">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-479">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c9fe1-480">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-480">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c9fe1-481">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-481">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9fe1-482">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c9fe1-482">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="c9fe1-483">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-483">Azure documentation</span></span>

* [<span data-ttu-id="c9fe1-484">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="c9fe1-484">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="c9fe1-485">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="c9fe1-485">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="c9fe1-486">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="c9fe1-486">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="c9fe1-487">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-487">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="c9fe1-488">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-488">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="c9fe1-489">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-489">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="c9fe1-490">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-490">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9fe1-491">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="c9fe1-491">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="c9fe1-492">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-492">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="c9fe1-493">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-493">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="c9fe1-494">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-494">Visual Studio documentation</span></span>

* [<span data-ttu-id="c9fe1-495">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-495">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="c9fe1-496">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-496">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="c9fe1-497">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-497">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="c9fe1-498">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-498">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="c9fe1-499">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-499">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="c9fe1-500">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-500">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="c9fe1-501">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-501">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="c9fe1-502">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-502">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="c9fe1-503">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-503">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="c9fe1-504">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-504">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="c9fe1-505">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-505">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="c9fe1-506">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-506">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="c9fe1-507">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-507">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="c9fe1-508">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-508">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="c9fe1-509">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-509">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="c9fe1-510">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c9fe1-510">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="c9fe1-511">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-511">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="c9fe1-512">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-512">App startup errors</span></span>

<span data-ttu-id="c9fe1-513">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-513">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="c9fe1-514">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 - 処理エラー*" または "*500.30 - 開始エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-514">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="c9fe1-515">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="c9fe1-515">403.14 Forbidden</span></span>

<span data-ttu-id="c9fe1-516">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-516">The app fails to start.</span></span> <span data-ttu-id="c9fe1-517">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-517">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="c9fe1-518">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-518">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="c9fe1-519">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-519">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="c9fe1-520">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-520">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="c9fe1-521">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-521">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="c9fe1-522">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-522">Perform the following steps:</span></span>

1. <span data-ttu-id="c9fe1-523">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-523">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9fe1-524">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-524">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="c9fe1-525">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-525">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="c9fe1-526">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-526">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="c9fe1-527">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-527">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="c9fe1-528">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-528">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="c9fe1-529">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-529">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="c9fe1-530">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-530">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="c9fe1-531">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-531">500 Internal Server Error</span></span>

<span data-ttu-id="c9fe1-532">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-532">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="c9fe1-533">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-533">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="c9fe1-534">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-534">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="c9fe1-535">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-535">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="c9fe1-536">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-536">From the server's perspective, that's correct.</span></span> <span data-ttu-id="c9fe1-537">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-537">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="c9fe1-538">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-538">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="c9fe1-539">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-539">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="c9fe1-540">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-540">The worker process fails.</span></span> <span data-ttu-id="c9fe1-541">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-541">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-542">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .NET Core CLR の検出に失敗し、インプロセス要求ハンドラー (*aspnetcorev2_inprocess.dll*) を検出します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-542">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="c9fe1-543">次の点をご確認ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-543">Check that:</span></span>

* <span data-ttu-id="c9fe1-544">アプリが [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet パッケージまたは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を対象としている。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-544">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="c9fe1-545">アプリが対象としているバージョンの ASP.NET Core 共有フレームワークが対象のコンピューターにインストールされている。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-545">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="c9fe1-546">500.0 アウト プロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-546">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="c9fe1-547">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-547">The worker process fails.</span></span> <span data-ttu-id="c9fe1-548">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-548">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-549">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は、アウト プロセスのホスティング要求ハンドラーの検索に失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-549">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="c9fe1-550">*aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* の隣のサブフォルダーにあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-550">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="c9fe1-551">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-551">502.5 Process Failure</span></span>

<span data-ttu-id="c9fe1-552">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-552">The worker process fails.</span></span> <span data-ttu-id="c9fe1-553">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-553">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-554">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-554">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="c9fe1-555">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-555">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="c9fe1-556">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-556">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="c9fe1-557">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-557">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="c9fe1-558">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-558">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="c9fe1-559">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-559">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="c9fe1-560">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-560">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="c9fe1-561">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-561">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="c9fe1-562">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="c9fe1-562">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="c9fe1-563">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-563">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="c9fe1-564">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-564">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="c9fe1-565">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-565">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="c9fe1-566">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-566">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="c9fe1-567">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-567">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="c9fe1-568">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-568">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="c9fe1-569">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-569">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="c9fe1-570">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-570">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="c9fe1-571">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-571">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="c9fe1-572">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="c9fe1-572">Connection reset</span></span>

<span data-ttu-id="c9fe1-573">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-573">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="c9fe1-574">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-574">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="c9fe1-575">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-575">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="c9fe1-576">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-576">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="c9fe1-577">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="c9fe1-577">Default startup limits</span></span>

<span data-ttu-id="c9fe1-578">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-578">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="c9fe1-579">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-579">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="c9fe1-580">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-580">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="c9fe1-581">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-581">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="c9fe1-582">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-582">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-583">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-583">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="c9fe1-584">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-584">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="c9fe1-585">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-585">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="c9fe1-586">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-586">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="c9fe1-587">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-587">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="c9fe1-588">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-588">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="c9fe1-589">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-589">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="c9fe1-590">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-590">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-591">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-591">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-592">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-592">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-593">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-593">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-594">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-594">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9fe1-595">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-595">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="c9fe1-596">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-596">Examine the log.</span></span> <span data-ttu-id="c9fe1-597">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-597">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="c9fe1-598">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-598">Run the app in the Kudu console</span></span>

<span data-ttu-id="c9fe1-599">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-599">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-600">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-600">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="c9fe1-601">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-601">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-602">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-602">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-603">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-603">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-604">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-604">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="c9fe1-605">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-605">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="c9fe1-606">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-606">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="c9fe1-607">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-607">Run the app:</span></span>
   * <span data-ttu-id="c9fe1-608">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-608">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="c9fe1-609">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-609">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="c9fe1-610">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-610">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9fe1-611">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-611">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9fe1-612">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="c9fe1-612">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9fe1-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-613">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9fe1-614">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-614">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9fe1-615">エラーを表示しているアプリからのコンソール出力は、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-615">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="c9fe1-616">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-616">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="c9fe1-617">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-617">**Current release**</span></span>

* <span data-ttu-id="c9fe1-618">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-618">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="c9fe1-619">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-619">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="c9fe1-620">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-620">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="c9fe1-621">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-621">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="c9fe1-622">エラーを表示しているアプリからのコンソール出力は、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-622">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9fe1-623">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-623">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9fe1-624">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="c9fe1-624">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9fe1-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-625">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9fe1-626">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-626">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9fe1-627">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-627">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="c9fe1-628">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-628">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-629">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-629">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-630">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-630">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9fe1-631">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-631">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9fe1-632">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-632">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="c9fe1-633">**[推奨されている解決方法]** > **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、 **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-633">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="c9fe1-634">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-634">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9fe1-635">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-635">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9fe1-636">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-636">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-637">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-637">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9fe1-638">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-638">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-639">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-639">Make a request to the app.</span></span>
1. <span data-ttu-id="c9fe1-640">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-640">Return to the Azure portal.</span></span> <span data-ttu-id="c9fe1-641">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-641">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9fe1-642">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-642">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-643">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-643">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-644">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-644">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-645">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-645">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9fe1-646">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-646">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="c9fe1-647">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-647">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="c9fe1-648">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-648">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9fe1-649">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-649">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="c9fe1-650">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-650">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="c9fe1-651">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-651">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9fe1-652">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-652">Select **Save** to save the file.</span></span>

<span data-ttu-id="c9fe1-653">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-653">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-654">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-654">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-655">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-655">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="c9fe1-656">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-656">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9fe1-657">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-657">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-658">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-658">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="c9fe1-659">ASP.NET Core モジュールのデバッグ ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-659">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-660">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-660">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="c9fe1-661">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-661">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9fe1-662">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-662">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="c9fe1-663">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-663">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="c9fe1-664">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-664">Redeploy the app.</span></span>
   * <span data-ttu-id="c9fe1-665">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-665">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="c9fe1-666">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-666">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-667">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-667">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-668">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-668">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="c9fe1-669">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-669">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="c9fe1-670">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-670">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9fe1-671">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-671">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="c9fe1-672">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-672">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="c9fe1-673">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-673">Select the **Save** button.</span></span>
1. <span data-ttu-id="c9fe1-674">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-674">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-675">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-675">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-676">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-676">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-677">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-677">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-678">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-678">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9fe1-679">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-679">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="c9fe1-680">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-680">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="c9fe1-681">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-681">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="c9fe1-682">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-682">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="c9fe1-683">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-683">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="c9fe1-684">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-684">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="c9fe1-685">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-685">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="c9fe1-686">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-686">Save the file.</span></span>

<span data-ttu-id="c9fe1-687">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-687">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-688">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-688">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-689">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-689">There's no limit on log file size.</span></span> <span data-ttu-id="c9fe1-690">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-690">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9fe1-691">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-691">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-692">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-692">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="c9fe1-693">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-693">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-694">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-694">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="c9fe1-695">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-695">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9fe1-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-696">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="c9fe1-697">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="c9fe1-697">Monitoring blades</span></span>

<span data-ttu-id="c9fe1-698">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-698">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="c9fe1-699">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-699">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="c9fe1-700">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-700">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="c9fe1-701">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-701">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="c9fe1-702">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-702">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="c9fe1-703">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-703">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="c9fe1-704">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-704">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="c9fe1-705">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-705">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="c9fe1-706">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-706">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c9fe1-707">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-707">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="c9fe1-708">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-708">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="c9fe1-709">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-709">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="c9fe1-710">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-710">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9fe1-711">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-711">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-712">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-712">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-713">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-713">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-714">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-714">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9fe1-715">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-715">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-716">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-716">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9fe1-717">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-717">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="c9fe1-718">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-718">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="c9fe1-719">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-719">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="c9fe1-720">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-720">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="c9fe1-721">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-721">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="c9fe1-722">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-722">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="c9fe1-723">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-723">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="c9fe1-724">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-724">Make a request to the app.</span></span>
1. <span data-ttu-id="c9fe1-725">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-725">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="c9fe1-726">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-726">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="c9fe1-727">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-727">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="c9fe1-728">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-728">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9fe1-729">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-729">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="c9fe1-730">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-730">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="c9fe1-731">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-731">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-732">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-732">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-733">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-733">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9fe1-734">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-734">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-735">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-735">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="c9fe1-736">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-736">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="c9fe1-737">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-737">Application Event Log (IIS)</span></span>

<span data-ttu-id="c9fe1-738">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-738">Access the Application Event Log:</span></span>

1. <span data-ttu-id="c9fe1-739">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-739">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c9fe1-740">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-740">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c9fe1-741">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-741">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c9fe1-742">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-742">Search for errors associated with the failing app.</span></span> <span data-ttu-id="c9fe1-743">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-743">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c9fe1-744">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-744">Run the app at a command prompt</span></span>

<span data-ttu-id="c9fe1-745">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-745">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-746">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-746">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="c9fe1-747">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="c9fe1-747">Framework-dependent deployment</span></span>

<span data-ttu-id="c9fe1-748">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-748">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="c9fe1-749">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-749">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="c9fe1-750">次のコマンドで、\<assembly_name> をアプリのアセンブリの名前に置き換えます。`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-750">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="c9fe1-751">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-751">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9fe1-752">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-752">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9fe1-753">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-753">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9fe1-754">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-754">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="c9fe1-755">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="c9fe1-755">Self-contained deployment</span></span>

<span data-ttu-id="c9fe1-756">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-756">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="c9fe1-757">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-757">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="c9fe1-758">次のコマンドで、\<assembly_name> をアプリのアセンブリの名前に置き換えます。`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-758">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="c9fe1-759">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-759">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9fe1-760">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-760">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9fe1-761">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-761">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9fe1-762">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-762">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="c9fe1-763">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-763">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="c9fe1-764">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-764">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9fe1-765">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-765">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9fe1-766">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-766">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="c9fe1-767">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-767">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="c9fe1-768">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-768">Edit the *web.config* file.</span></span> <span data-ttu-id="c9fe1-769">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-769">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="c9fe1-770">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-770">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="c9fe1-771">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-771">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="c9fe1-772">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-772">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="c9fe1-773">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-773">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="c9fe1-774">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-774">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-775">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-775">Make a request to the app.</span></span>
1. <span data-ttu-id="c9fe1-776">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-776">Navigate to the *logs* folder.</span></span> <span data-ttu-id="c9fe1-777">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-777">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="c9fe1-778">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-778">Study the log for errors.</span></span>

<span data-ttu-id="c9fe1-779">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-779">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9fe1-780">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-780">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-781">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-781">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9fe1-782">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-782">Save the file.</span></span>

<span data-ttu-id="c9fe1-783">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-783">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-784">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-784">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-785">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-785">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9fe1-786">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-786">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-787">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-787">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="c9fe1-788">ASP.NET Core モジュールのデバッグ ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-788">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="c9fe1-789">ASP.NET Core モジュールのデバッグ ログを有効にするには、次のハンドラー設定をアプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-789">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="c9fe1-790">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-790">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="c9fe1-791">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-791">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="c9fe1-792">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-792">Enable the Developer Exception Page</span></span>

<span data-ttu-id="c9fe1-793">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-793">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="c9fe1-794">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-794">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="c9fe1-795">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-795">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="c9fe1-796">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-796">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="c9fe1-797">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-797">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="c9fe1-798">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-798">Obtain data from an app</span></span>

<span data-ttu-id="c9fe1-799">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-799">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="c9fe1-800">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-800">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="c9fe1-801">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-801">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="c9fe1-802">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-802">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c9fe1-803">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="c9fe1-803">App crashes or encounters an exception</span></span>

<span data-ttu-id="c9fe1-804">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-804">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c9fe1-805">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-805">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="c9fe1-806">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-806">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="c9fe1-807">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-807">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="c9fe1-808">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-808">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="c9fe1-809">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-809">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="c9fe1-810">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-810">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c9fe1-811">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-811">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="c9fe1-812">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-812">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="c9fe1-813">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-813">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="c9fe1-814">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-814">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c9fe1-815">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-815">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-816">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-816">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c9fe1-817">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-817">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c9fe1-818">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-818">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c9fe1-819">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-819">Analyze the dump</span></span>

<span data-ttu-id="c9fe1-820">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-820">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c9fe1-821">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-821">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="c9fe1-822">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-822">Clear package caches</span></span>

<span data-ttu-id="c9fe1-823">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-823">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c9fe1-824">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-824">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c9fe1-825">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-825">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c9fe1-826">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-826">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c9fe1-827">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-827">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c9fe1-828">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-828">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c9fe1-829">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-829">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c9fe1-830">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-830">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c9fe1-831">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-831">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9fe1-832">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c9fe1-832">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="c9fe1-833">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-833">Azure documentation</span></span>

* [<span data-ttu-id="c9fe1-834">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="c9fe1-834">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="c9fe1-835">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="c9fe1-835">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="c9fe1-836">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="c9fe1-836">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="c9fe1-837">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-837">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="c9fe1-838">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-838">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="c9fe1-839">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-839">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="c9fe1-840">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-840">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9fe1-841">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="c9fe1-841">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="c9fe1-842">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-842">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="c9fe1-843">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-843">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="c9fe1-844">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-844">Visual Studio documentation</span></span>

* [<span data-ttu-id="c9fe1-845">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-845">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="c9fe1-846">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-846">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="c9fe1-847">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-847">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="c9fe1-848">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-848">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="c9fe1-849">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-849">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="c9fe1-850">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-850">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="c9fe1-851">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-851">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="c9fe1-852">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-852">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="c9fe1-853">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-853">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="c9fe1-854">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-854">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="c9fe1-855">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-855">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="c9fe1-856">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-856">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="c9fe1-857">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-857">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="c9fe1-858">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-858">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="c9fe1-859">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-859">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="c9fe1-860">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="c9fe1-860">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="c9fe1-861">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-861">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="c9fe1-862">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-862">App startup errors</span></span>

<span data-ttu-id="c9fe1-863">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-863">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="c9fe1-864">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 処理エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-864">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="c9fe1-865">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="c9fe1-865">403.14 Forbidden</span></span>

<span data-ttu-id="c9fe1-866">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-866">The app fails to start.</span></span> <span data-ttu-id="c9fe1-867">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-867">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="c9fe1-868">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-868">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="c9fe1-869">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-869">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="c9fe1-870">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-870">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="c9fe1-871">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-871">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="c9fe1-872">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-872">Perform the following steps:</span></span>

1. <span data-ttu-id="c9fe1-873">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-873">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9fe1-874">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-874">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="c9fe1-875">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-875">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="c9fe1-876">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-876">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="c9fe1-877">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-877">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="c9fe1-878">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-878">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="c9fe1-879">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-879">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="c9fe1-880">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-880">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="c9fe1-881">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-881">500 Internal Server Error</span></span>

<span data-ttu-id="c9fe1-882">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-882">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="c9fe1-883">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-883">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="c9fe1-884">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-884">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="c9fe1-885">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-885">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="c9fe1-886">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-886">From the server's perspective, that's correct.</span></span> <span data-ttu-id="c9fe1-887">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-887">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="c9fe1-888">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-888">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="c9fe1-889">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="c9fe1-889">502.5 Process Failure</span></span>

<span data-ttu-id="c9fe1-890">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-890">The worker process fails.</span></span> <span data-ttu-id="c9fe1-891">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-891">The app doesn't start.</span></span>

<span data-ttu-id="c9fe1-892">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-892">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="c9fe1-893">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-893">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="c9fe1-894">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-894">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="c9fe1-895">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-895">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="c9fe1-896">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-896">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="c9fe1-897">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-897">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="c9fe1-898">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-898">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="c9fe1-899">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-899">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="c9fe1-900">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="c9fe1-900">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="c9fe1-901">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-901">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="c9fe1-902">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-902">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="c9fe1-903">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-903">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="c9fe1-904">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-904">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="c9fe1-905">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-905">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="c9fe1-906">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-906">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="c9fe1-907">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-907">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="c9fe1-908">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-908">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="c9fe1-909">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-909">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="c9fe1-910">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="c9fe1-910">Connection reset</span></span>

<span data-ttu-id="c9fe1-911">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-911">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="c9fe1-912">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-912">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="c9fe1-913">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-913">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="c9fe1-914">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-914">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="c9fe1-915">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="c9fe1-915">Default startup limits</span></span>

<span data-ttu-id="c9fe1-916">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-916">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="c9fe1-917">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-917">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="c9fe1-918">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-918">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="c9fe1-919">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-919">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="c9fe1-920">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-920">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-921">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-921">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="c9fe1-922">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-922">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="c9fe1-923">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-923">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="c9fe1-924">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-924">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="c9fe1-925">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-925">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="c9fe1-926">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-926">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="c9fe1-927">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-927">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="c9fe1-928">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-928">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-929">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-929">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-930">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-930">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-931">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-931">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-932">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-932">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9fe1-933">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-933">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="c9fe1-934">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-934">Examine the log.</span></span> <span data-ttu-id="c9fe1-935">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-935">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="c9fe1-936">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-936">Run the app in the Kudu console</span></span>

<span data-ttu-id="c9fe1-937">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-937">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-938">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-938">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="c9fe1-939">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-939">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="c9fe1-940">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-940">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-941">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-941">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-942">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-942">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="c9fe1-943">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-943">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="c9fe1-944">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-944">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="c9fe1-945">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-945">Run the app:</span></span>
   * <span data-ttu-id="c9fe1-946">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-946">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="c9fe1-947">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-947">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="c9fe1-948">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-948">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9fe1-949">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-949">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9fe1-950">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="c9fe1-950">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9fe1-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-951">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9fe1-952">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-952">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9fe1-953">エラーを表示しているアプリからのコンソール出力は、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-953">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="c9fe1-954">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-954">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="c9fe1-955">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-955">**Current release**</span></span>

* <span data-ttu-id="c9fe1-956">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-956">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="c9fe1-957">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-957">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="c9fe1-958">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-958">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="c9fe1-959">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-959">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="c9fe1-960">エラーを表示しているアプリからのコンソール出力は、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-960">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="c9fe1-961">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="c9fe1-961">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="c9fe1-962">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="c9fe1-962">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="c9fe1-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-963">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="c9fe1-964">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="c9fe1-964">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="c9fe1-965">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-965">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="c9fe1-966">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-966">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-967">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-967">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-968">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-968">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9fe1-969">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-969">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9fe1-970">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-970">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="c9fe1-971">**[推奨されている解決方法]** > **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、 **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-971">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="c9fe1-972">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-972">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="c9fe1-973">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-973">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9fe1-974">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-974">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-975">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-975">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9fe1-976">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-976">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-977">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-977">Make a request to the app.</span></span>
1. <span data-ttu-id="c9fe1-978">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-978">Return to the Azure portal.</span></span> <span data-ttu-id="c9fe1-979">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-979">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9fe1-980">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-980">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-981">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-981">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-982">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-982">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-983">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-983">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="c9fe1-984">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-984">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="c9fe1-985">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-985">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="c9fe1-986">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-986">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9fe1-987">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-987">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="c9fe1-988">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-988">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="c9fe1-989">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-989">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9fe1-990">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-990">Select **Save** to save the file.</span></span>

<span data-ttu-id="c9fe1-991">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-991">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-992">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-992">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-993">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-993">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="c9fe1-994">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-994">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="c9fe1-995">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-995">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-996">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-996">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="c9fe1-997">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-997">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="c9fe1-998">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-998">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="c9fe1-999">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-999">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9fe1-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1000">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="c9fe1-1001">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1001">Monitoring blades</span></span>

<span data-ttu-id="c9fe1-1002">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1002">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="c9fe1-1003">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1003">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="c9fe1-1004">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1004">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="c9fe1-1005">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1005">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="c9fe1-1006">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1006">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="c9fe1-1007">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1007">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="c9fe1-1008">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1008">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="c9fe1-1009">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1009">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="c9fe1-1010">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1010">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="c9fe1-1011">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1011">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="c9fe1-1012">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1012">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="c9fe1-1013">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1013">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="c9fe1-1014">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1014">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="c9fe1-1015">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1015">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="c9fe1-1016">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1016">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="c9fe1-1017">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1017">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="c9fe1-1018">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1018">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="c9fe1-1019">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1019">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-1020">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1020">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="c9fe1-1021">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1021">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="c9fe1-1022">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1022">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="c9fe1-1023">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1023">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="c9fe1-1024">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1024">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="c9fe1-1025">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1025">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="c9fe1-1026">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1026">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="c9fe1-1027">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1027">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="c9fe1-1028">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1028">Make a request to the app.</span></span>
1. <span data-ttu-id="c9fe1-1029">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1029">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="c9fe1-1030">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1030">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="c9fe1-1031">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1031">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="c9fe1-1032">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1032">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="c9fe1-1033">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1033">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="c9fe1-1034">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1034">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="c9fe1-1035">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1035">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-1036">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1036">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-1037">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1037">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9fe1-1038">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1038">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-1039">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1039">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="c9fe1-1040">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1040">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="c9fe1-1041">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1041">Application Event Log (IIS)</span></span>

<span data-ttu-id="c9fe1-1042">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1042">Access the Application Event Log:</span></span>

1. <span data-ttu-id="c9fe1-1043">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1043">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="c9fe1-1044">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1044">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="c9fe1-1045">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1045">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="c9fe1-1046">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1046">Search for errors associated with the failing app.</span></span> <span data-ttu-id="c9fe1-1047">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1047">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="c9fe1-1048">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1048">Run the app at a command prompt</span></span>

<span data-ttu-id="c9fe1-1049">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1049">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="c9fe1-1050">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1050">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="c9fe1-1051">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1051">Framework-dependent deployment</span></span>

<span data-ttu-id="c9fe1-1052">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1052">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="c9fe1-1053">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1053">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="c9fe1-1054">次のコマンドで、\<assembly_name> をアプリのアセンブリの名前に置き換えます。`dotnet .\<assembly_name>.dll`。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1054">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="c9fe1-1055">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1055">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9fe1-1056">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1056">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9fe1-1057">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1057">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9fe1-1058">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1058">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="c9fe1-1059">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1059">Self-contained deployment</span></span>

<span data-ttu-id="c9fe1-1060">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1060">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="c9fe1-1061">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1061">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="c9fe1-1062">次のコマンドで、\<assembly_name> をアプリのアセンブリの名前に置き換えます。`<assembly_name>.exe`。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1062">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="c9fe1-1063">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1063">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="c9fe1-1064">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1064">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="c9fe1-1065">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1065">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="c9fe1-1066">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1066">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="c9fe1-1067">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1067">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="c9fe1-1068">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1068">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="c9fe1-1069">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1069">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="c9fe1-1070">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1070">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="c9fe1-1071">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1071">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="c9fe1-1072">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1072">Edit the *web.config* file.</span></span> <span data-ttu-id="c9fe1-1073">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1073">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="c9fe1-1074">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1074">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="c9fe1-1075">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1075">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="c9fe1-1076">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1076">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="c9fe1-1077">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1077">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="c9fe1-1078">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1078">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-1079">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1079">Make a request to the app.</span></span>
1. <span data-ttu-id="c9fe1-1080">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1080">Navigate to the *logs* folder.</span></span> <span data-ttu-id="c9fe1-1081">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1081">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="c9fe1-1082">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1082">Study the log for errors.</span></span>

<span data-ttu-id="c9fe1-1083">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1083">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="c9fe1-1084">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1084">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="c9fe1-1085">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1085">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="c9fe1-1086">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1086">Save the file.</span></span>

<span data-ttu-id="c9fe1-1087">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1087">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-1088">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1088">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="c9fe1-1089">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1089">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="c9fe1-1090">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1090">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="c9fe1-1091">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1091">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="c9fe1-1092">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1092">Enable the Developer Exception Page</span></span>

<span data-ttu-id="c9fe1-1093">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1093">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="c9fe1-1094">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1094">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

<span data-ttu-id="c9fe1-1095">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1095">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="c9fe1-1096">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1096">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="c9fe1-1097">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1097">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="c9fe1-1098">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1098">Obtain data from an app</span></span>

<span data-ttu-id="c9fe1-1099">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1099">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="c9fe1-1100">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1100">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="c9fe1-1101">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1101">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="c9fe1-1102">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1102">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="c9fe1-1103">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1103">App crashes or encounters an exception</span></span>

<span data-ttu-id="c9fe1-1104">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1104">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="c9fe1-1105">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1105">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="c9fe1-1106">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1106">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="c9fe1-1107">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1107">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="c9fe1-1108">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1108">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="c9fe1-1109">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1109">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="c9fe1-1110">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1110">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="c9fe1-1111">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1111">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="c9fe1-1112">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1112">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="c9fe1-1113">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1113">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="c9fe1-1114">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1114">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="c9fe1-1115">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1115">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="c9fe1-1116">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1116">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="c9fe1-1117">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1117">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="c9fe1-1118">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1118">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="c9fe1-1119">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1119">Analyze the dump</span></span>

<span data-ttu-id="c9fe1-1120">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1120">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="c9fe1-1121">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1121">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="c9fe1-1122">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1122">Clear package caches</span></span>

<span data-ttu-id="c9fe1-1123">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1123">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="c9fe1-1124">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1124">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="c9fe1-1125">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1125">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="c9fe1-1126">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1126">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="c9fe1-1127">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1127">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="c9fe1-1128">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1128">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="c9fe1-1129">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1129">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="c9fe1-1130">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1130">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="c9fe1-1131">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1131">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9fe1-1132">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1132">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="c9fe1-1133">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1133">Azure documentation</span></span>

* [<span data-ttu-id="c9fe1-1134">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1134">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="c9fe1-1135">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1135">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="c9fe1-1136">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1136">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="c9fe1-1137">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1137">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="c9fe1-1138">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1138">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="c9fe1-1139">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1139">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="c9fe1-1140">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1140">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="c9fe1-1141">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1141">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="c9fe1-1142">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1142">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="c9fe1-1143">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1143">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="c9fe1-1144">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1144">Visual Studio documentation</span></span>

* [<span data-ttu-id="c9fe1-1145">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1145">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="c9fe1-1146">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1146">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="c9fe1-1147">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1147">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="c9fe1-1148">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1148">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="c9fe1-1149">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="c9fe1-1149">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
