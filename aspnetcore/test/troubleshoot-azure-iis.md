---
title: Azure App Service および IIS での ASP.NET Core のトラブルシューティング
author: rick-anderson
description: ASP.NET Core アプリの Azure App Service およびインターネット インフォメーション サービス (IIS) のデプロイに関する問題を診断する方法について学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: f994cd1274bda9082a7cd8b637968b2769db1671
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661708"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a><span data-ttu-id="3f504-103">Azure App Service および IIS での ASP.NET Core のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-103">Troubleshoot ASP.NET Core on Azure App Service and IIS</span></span>

<span data-ttu-id="3f504-104">作成者: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="3f504-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3f504-105">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-105">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="3f504-106">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-106">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="3f504-107">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3f504-107">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="3f504-108">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-108">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="3f504-109">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-109">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="3f504-110">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-110">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="3f504-111">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-111">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="3f504-112">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-112">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="3f504-113">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="3f504-113">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="3f504-114">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3f504-114">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="3f504-115">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3f504-115">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="3f504-116">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-116">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="3f504-117">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-117">App startup errors</span></span>

<span data-ttu-id="3f504-118">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="3f504-118">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="3f504-119">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 - 処理エラー*" または "*500.30 - 開始エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-119">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="3f504-120">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="3f504-120">403.14 Forbidden</span></span>

<span data-ttu-id="3f504-121">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="3f504-121">The app fails to start.</span></span> <span data-ttu-id="3f504-122">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-122">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="3f504-123">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-123">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="3f504-124">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="3f504-124">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="3f504-125">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="3f504-125">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="3f504-126">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-126">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="3f504-127">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="3f504-127">Perform the following steps:</span></span>

1. <span data-ttu-id="3f504-128">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-128">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="3f504-129">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="3f504-129">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="3f504-130">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-130">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="3f504-131">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-131">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="3f504-132">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-132">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="3f504-133">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-133">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="3f504-134">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-134">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="3f504-135">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-135">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="3f504-136">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="3f504-136">500 Internal Server Error</span></span>

<span data-ttu-id="3f504-137">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="3f504-137">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="3f504-138">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-138">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="3f504-139">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-139">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="3f504-140">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-140">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="3f504-141">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="3f504-141">From the server's perspective, that's correct.</span></span> <span data-ttu-id="3f504-142">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="3f504-142">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="3f504-143">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-143">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="3f504-144">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-144">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="3f504-145">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-145">The worker process fails.</span></span> <span data-ttu-id="3f504-146">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-146">The app doesn't start.</span></span>

<span data-ttu-id="3f504-147">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module) コンポーネントの読み込み中に不明なエラーが発生しました。</span><span class="sxs-lookup"><span data-stu-id="3f504-147">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="3f504-148">次のいずれかのアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-148">Take one of the following actions:</span></span>

* <span data-ttu-id="3f504-149">[Microsoft サポート](https://support.microsoft.com/oas/default.aspx?prid=15832)に問い合わせます ( **[開発者ツール]** 、 **[ASP.NET Core]** の順に選択します)。</span><span class="sxs-lookup"><span data-stu-id="3f504-149">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="3f504-150">Stack Overflow について質問します。</span><span class="sxs-lookup"><span data-stu-id="3f504-150">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="3f504-151">[GitHub リポジトリ](https://github.com/dotnet/AspNetCore)で問題を報告します。</span><span class="sxs-lookup"><span data-stu-id="3f504-151">File an issue on our [GitHub repository](https://github.com/dotnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="3f504-152">500.30 インプロセス起動エラー</span><span class="sxs-lookup"><span data-stu-id="3f504-152">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="3f504-153">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-153">The worker process fails.</span></span> <span data-ttu-id="3f504-154">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-154">The app doesn't start.</span></span>

<span data-ttu-id="3f504-155">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .NET Core CLR の開始をインプロセスで試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-155">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="3f504-156">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-156">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="3f504-157">一般的なエラー条件:</span><span class="sxs-lookup"><span data-stu-id="3f504-157">Common failure conditions:</span></span>

* <span data-ttu-id="3f504-158">存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="3f504-158">The app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="3f504-159">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-159">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>
* <span data-ttu-id="3f504-160">Azure Key Vault を使用している場合、Key Vault へのアクセス許可がありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-160">Using Azure Key Vault, lack of permissions to the Key Vault.</span></span> <span data-ttu-id="3f504-161">対象の Key Vault のアクセス ポリシーを確認して、正しいアクセス許可が付与されていることを確実にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-161">Check the access policies in the targeted Key Vault to ensure that the correct permissions are granted.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="3f504-162">500.31 ANCM Failed to Find Native Dependencies (500.31 ANCM ネイティブの依存関係を見つけられませんでした)</span><span class="sxs-lookup"><span data-stu-id="3f504-162">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="3f504-163">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-163">The worker process fails.</span></span> <span data-ttu-id="3f504-164">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-164">The app doesn't start.</span></span>

<span data-ttu-id="3f504-165">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)で .NET Core ランタイムの開始がインプロセスで試行されますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-165">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="3f504-166">このスタートアップ エラーの最も一般的な原因は、`Microsoft.NETCore.App` または `Microsoft.AspNetCore.App` ランタイムがインストールされていない場合です。</span><span class="sxs-lookup"><span data-stu-id="3f504-166">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="3f504-167">アプリが ASP.NET Core 3.0 をターゲットとして展開されていて、そのバージョンがコンピューターに存在しない場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-167">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="3f504-168">エラー メッセージの例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="3f504-168">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="3f504-169">エラー メッセージには、インストールされているすべての .NET Core のバージョンとアプリに必要なバージョンが一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-169">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="3f504-170">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-170">To fix this error, either:</span></span>

* <span data-ttu-id="3f504-171">適切なバージョンの .NET Core をマシンにインストールします。</span><span class="sxs-lookup"><span data-stu-id="3f504-171">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="3f504-172">マシンに存在する .NET Core のバージョンをターゲットにするようにアプリを変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-172">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="3f504-173">アプリを[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)として発行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-173">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="3f504-174">開発環境で実行している場合 (`ASPNETCORE_ENVIRONMENT` 環境変数が `Development` に設定されている場合)、特定のエラーが HTTP 応答に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-174">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="3f504-175">プロセスのスタートアップ エラーの原因は、アプリケーション イベント ログでも見つかります。</span><span class="sxs-lookup"><span data-stu-id="3f504-175">The cause of a process startup failure is also found in the Application Event Log.</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="3f504-176">500.32 ANCM Failed to Load dll (500.32 ANCM DLL を読み込めませんでした)</span><span class="sxs-lookup"><span data-stu-id="3f504-176">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="3f504-177">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-177">The worker process fails.</span></span> <span data-ttu-id="3f504-178">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-178">The app doesn't start.</span></span>

<span data-ttu-id="3f504-179">このエラーの最も一般的な原因は、アプリが互換性のないプロセッサ アーキテクチャ用に発行されていることです。</span><span class="sxs-lookup"><span data-stu-id="3f504-179">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="3f504-180">ワーカー プロセスが 32 ビットアプリとして実行されていて、そのアプリが 64 ビットをターゲットとして発行されている場合、このエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-180">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="3f504-181">このエラーを修正するには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-181">To fix this error, either:</span></span>

* <span data-ttu-id="3f504-182">ワーカー プロセスと同じプロセッサ アーキテクチャ用にアプリを再発行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-182">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="3f504-183">アプリを[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-executables-fde)として発行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-183">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="3f504-184">500.33 ANCM Request Handler Load Failure (500.33 ANCM 要求ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="3f504-184">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="3f504-185">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-185">The worker process fails.</span></span> <span data-ttu-id="3f504-186">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-186">The app doesn't start.</span></span>

<span data-ttu-id="3f504-187">アプリは `Microsoft.AspNetCore.App` フレームワークを参照していませんでした。</span><span class="sxs-lookup"><span data-stu-id="3f504-187">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="3f504-188">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)でホストできるのは、`Microsoft.AspNetCore.App` フレームワークをターゲットとしているアプリのみです。</span><span class="sxs-lookup"><span data-stu-id="3f504-188">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="3f504-189">このエラーを修正するには、アプリが `Microsoft.AspNetCore.App` フレームワークをターゲットにしていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-189">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="3f504-190">アプリがターゲットとしているフレームワークを確認するには、`.runtimeconfig.json` を確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-190">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="3f504-191">500.34 ANCM Mixed Hosting Models Not Supported (500.34 ANCM 混在ホスティング モデルはサポートされません)</span><span class="sxs-lookup"><span data-stu-id="3f504-191">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="3f504-192">ワーカー プロセスでは、インプロセス アプリとアウト プロセス アプリの両方を同じプロセスで実行できません。</span><span class="sxs-lookup"><span data-stu-id="3f504-192">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="3f504-193">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-193">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="3f504-194">500.35 ANCM Multiple In-Process Applications in same Process (500.35 ANCM 同一プロセス内の複数のインプロセス アプリケーション)</span><span class="sxs-lookup"><span data-stu-id="3f504-194">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="3f504-195">ワーカー プロセスによって、同じプロセスで複数のインプロセス アプリを実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="3f504-195">The worker process can't run multiple in-process apps in the same process.</span></span>

<span data-ttu-id="3f504-196">このエラーを修正するには、別の IIS アプリケーション プールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-196">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="3f504-197">500.36 ANCM Out-Of-Process Handler Load Failure (500.36 ANCM アウト プロセス ハンドラーの読み込みエラー)</span><span class="sxs-lookup"><span data-stu-id="3f504-197">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="3f504-198">アウト プロセス要求ハンドラーの *aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* ファイルの次にありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-198">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="3f504-199">これは、[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)のインストールが破損していることを示しています。</span><span class="sxs-lookup"><span data-stu-id="3f504-199">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="3f504-200">このエラーを修正するには、[.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS 用) または Visual Studio (IIS Express 用) のインストールを修復します。</span><span class="sxs-lookup"><span data-stu-id="3f504-200">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="3f504-201">500.37 ANCM Failed to Start Within Startup Time Limit (500.37 ANCM スタートアップ時間の制限内に起動できませんでした)</span><span class="sxs-lookup"><span data-stu-id="3f504-201">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="3f504-202">指定されたスタートアップ時間の制限内に ANCM が起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="3f504-202">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="3f504-203">既定では、タイムアウトは 120 秒です。</span><span class="sxs-lookup"><span data-stu-id="3f504-203">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="3f504-204">このエラーは、同じマシン上で多数のアプリを起動したときに発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-204">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="3f504-205">スタートアップ中のサーバー上の CPU/メモリ使用量の急上昇を確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-205">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="3f504-206">必要に応じて、複数のアプリのスタートアップ プロセスをずらします。</span><span class="sxs-lookup"><span data-stu-id="3f504-206">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50038-ancm-application-dll-not-found"></a><span data-ttu-id="3f504-207">500.38 ANCM アプリケーション DLL が見つかりません</span><span class="sxs-lookup"><span data-stu-id="3f504-207">500.38 ANCM Application DLL Not Found</span></span>

<span data-ttu-id="3f504-208">ANCM は、実行可能ファイルの横にあるアプリケーション DLL を見つけることができませんでした。</span><span class="sxs-lookup"><span data-stu-id="3f504-208">ANCM failed to locate the application DLL, which should be next to the executable.</span></span>

<span data-ttu-id="3f504-209">このエラーは、インプロセス ホスティング モデルを使用して[単一ファイルの実行可能ファイル](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables)としてパッケージ化されたアプリをホストするときに発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-209">This error occurs when hosting an app packaged as a [single-file executable](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) using the in-process hosting model.</span></span> <span data-ttu-id="3f504-210">インプロセス モデルでは、ANCM によって既存の IIS プロセスに .NET Core アプリが読み込まれることが必要です。</span><span class="sxs-lookup"><span data-stu-id="3f504-210">The in-process model requires that the ANCM load the .NET Core app into the existing IIS process.</span></span> <span data-ttu-id="3f504-211">このシナリオは、単一ファイル展開モデルではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3f504-211">This scenario isn't supported by the single-file deployment model.</span></span> <span data-ttu-id="3f504-212">このエラーを修正するには、アプリのプロジェクト ファイルで、次のうち **1 つ**を使用します。</span><span class="sxs-lookup"><span data-stu-id="3f504-212">Use **one** of the following approaches in the app's project file to fix this error:</span></span>

1. <span data-ttu-id="3f504-213">`PublishSingleFile` MSBuild プロパティを `false` に設定して、単一ファイルの公開を無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-213">Disable single-file publishing by setting the `PublishSingleFile` MSBuild property to `false`.</span></span>
1. <span data-ttu-id="3f504-214">`AspNetCoreHostingModel` MSBuild プロパティを `OutOfProcess` に設定して、アウトプロセス ホスティング モデルに切り替えます。</span><span class="sxs-lookup"><span data-stu-id="3f504-214">Switch to the out-of-process hosting model by setting the `AspNetCoreHostingModel` MSBuild property to `OutOfProcess`.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="3f504-215">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="3f504-215">502.5 Process Failure</span></span>

<span data-ttu-id="3f504-216">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-216">The worker process fails.</span></span> <span data-ttu-id="3f504-217">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-217">The app doesn't start.</span></span>

<span data-ttu-id="3f504-218">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-218">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="3f504-219">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-219">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="3f504-220">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="3f504-220">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="3f504-221">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-221">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="3f504-222">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-222">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="3f504-223">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-223">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="3f504-224">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-224">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="3f504-225">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-225">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="3f504-226">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="3f504-226">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="3f504-227">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="3f504-227">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="3f504-228">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-228">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="3f504-229">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-229">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="3f504-230">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-230">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="3f504-231">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-231">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="3f504-232">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-232">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="3f504-233">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-233">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="3f504-234">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-234">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="3f504-235">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-235">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="3f504-236">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="3f504-236">Connection reset</span></span>

<span data-ttu-id="3f504-237">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="3f504-237">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="3f504-238">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-238">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="3f504-239">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-239">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="3f504-240">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-240">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="3f504-241">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="3f504-241">Default startup limits</span></span>

<span data-ttu-id="3f504-242">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-242">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="3f504-243">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="3f504-243">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="3f504-244">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-244">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="3f504-245">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-245">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="3f504-246">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-246">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="3f504-247">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-247">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="3f504-248">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-248">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="3f504-249">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-249">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="3f504-250">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-250">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="3f504-251">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-251">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="3f504-252">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-252">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="3f504-253">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="3f504-253">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="3f504-254">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-254">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-255">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-255">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-256">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-256">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-257">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-257">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-258">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-258">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="3f504-259">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-259">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="3f504-260">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-260">Examine the log.</span></span> <span data-ttu-id="3f504-261">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-261">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="3f504-262">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-262">Run the app in the Kudu console</span></span>

<span data-ttu-id="3f504-263">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="3f504-263">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="3f504-264">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="3f504-264">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="3f504-265">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-265">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-266">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-266">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-267">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-267">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-268">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-268">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="3f504-269">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3f504-269">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="3f504-270">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="3f504-270">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="3f504-271">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-271">Run the app:</span></span>
   * <span data-ttu-id="3f504-272">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-272">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="3f504-273">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-273">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="3f504-274">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-274">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="3f504-275">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="3f504-275">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="3f504-276">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="3f504-276">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="3f504-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="3f504-277">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="3f504-278">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-278">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="3f504-279">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-279">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="3f504-280">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3f504-280">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="3f504-281">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="3f504-281">**Current release**</span></span>

* <span data-ttu-id="3f504-282">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-282">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="3f504-283">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-283">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="3f504-284">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-284">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="3f504-285">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="3f504-285">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="3f504-286">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-286">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="3f504-287">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="3f504-287">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="3f504-288">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="3f504-288">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="3f504-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="3f504-289">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="3f504-290">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-290">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="3f504-291">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-291">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="3f504-292">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-292">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="3f504-293">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-293">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="3f504-294">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3f504-294">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3f504-295">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-295">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="3f504-296">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-296">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="3f504-297">**[推奨されている解決方法]** > **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、 **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-297">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="3f504-298">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-298">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3f504-299">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-299">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="3f504-300">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="3f504-300">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-301">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-301">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="3f504-302">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-302">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="3f504-303">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-303">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-304">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="3f504-304">Return to the Azure portal.</span></span> <span data-ttu-id="3f504-305">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-305">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="3f504-306">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-306">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-307">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-307">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-308">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-308">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-309">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-309">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="3f504-310">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-310">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="3f504-311">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-311">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="3f504-312">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-312">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="3f504-313">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-313">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="3f504-314">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-314">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="3f504-315">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-315">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="3f504-316">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-316">Select **Save** to save the file.</span></span>

<span data-ttu-id="3f504-317">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-317">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-318">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-318">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-319">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-319">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="3f504-320">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="3f504-320">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="3f504-321">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-321">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-322">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-322">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="3f504-323">ASP.NET Core モジュールのデバッグ ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-323">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="3f504-324">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-324">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="3f504-325">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3f504-325">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3f504-326">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-326">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="3f504-327">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-327">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="3f504-328">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="3f504-328">Redeploy the app.</span></span>
   * <span data-ttu-id="3f504-329">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="3f504-329">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="3f504-330">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-330">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-331">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-331">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-332">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-332">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="3f504-333">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-333">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="3f504-334">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-334">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3f504-335">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-335">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="3f504-336">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="3f504-336">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="3f504-337">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-337">Select the **Save** button.</span></span>
1. <span data-ttu-id="3f504-338">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-338">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-339">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-339">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-340">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-340">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-341">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-341">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-342">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-342">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3f504-343">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-343">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="3f504-344">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-344">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="3f504-345">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-345">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="3f504-346">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-346">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="3f504-347">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-347">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="3f504-348">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="3f504-348">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="3f504-349">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-349">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="3f504-350">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-350">Save the file.</span></span>

<span data-ttu-id="3f504-351">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-351">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-352">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-352">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-353">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-353">There's no limit on log file size.</span></span> <span data-ttu-id="3f504-354">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="3f504-354">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="3f504-355">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-355">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-356">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-356">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="3f504-357">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-357">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="3f504-358">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-358">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="3f504-359">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-359">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="3f504-360">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="3f504-360">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="3f504-361">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="3f504-361">Monitoring blades</span></span>

<span data-ttu-id="3f504-362">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-362">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="3f504-363">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-363">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="3f504-364">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-364">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="3f504-365">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="3f504-365">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="3f504-366">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-366">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="3f504-367">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-367">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="3f504-368">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-368">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="3f504-369">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-369">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="3f504-370">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="3f504-370">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="3f504-371">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-371">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="3f504-372">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-372">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="3f504-373">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="3f504-373">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="3f504-374">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-374">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="3f504-375">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-375">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-376">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-376">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-377">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-377">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-378">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-378">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="3f504-379">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="3f504-379">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-380">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-380">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="3f504-381">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-381">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="3f504-382">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="3f504-382">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="3f504-383">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-383">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="3f504-384">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-384">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="3f504-385">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-385">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="3f504-386">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-386">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="3f504-387">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-387">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="3f504-388">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-388">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-389">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-389">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="3f504-390">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="3f504-390">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="3f504-391">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="3f504-391">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="3f504-392">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-392">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="3f504-393">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-393">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="3f504-394">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-394">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="3f504-395">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-395">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-396">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-396">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-397">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-397">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="3f504-398">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-398">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-399">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-399">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="3f504-400">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-400">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="3f504-401">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-401">Application Event Log (IIS)</span></span>

<span data-ttu-id="3f504-402">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="3f504-402">Access the Application Event Log:</span></span>

1. <span data-ttu-id="3f504-403">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-403">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="3f504-404">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-404">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="3f504-405">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-405">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="3f504-406">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="3f504-406">Search for errors associated with the failing app.</span></span> <span data-ttu-id="3f504-407">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-407">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="3f504-408">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="3f504-408">Run the app at a command prompt</span></span>

<span data-ttu-id="3f504-409">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="3f504-409">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="3f504-410">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-410">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="3f504-411">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="3f504-411">Framework-dependent deployment</span></span>

<span data-ttu-id="3f504-412">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-412">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="3f504-413">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-413">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="3f504-414">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-414">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="3f504-415">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-415">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="3f504-416">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-416">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="3f504-417">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="3f504-417">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="3f504-418">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="3f504-418">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="3f504-419">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="3f504-419">Self-contained deployment</span></span>

<span data-ttu-id="3f504-420">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-420">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="3f504-421">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-421">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="3f504-422">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-422">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="3f504-423">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-423">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="3f504-424">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-424">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="3f504-425">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="3f504-425">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="3f504-426">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="3f504-426">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="3f504-427">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-427">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="3f504-428">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3f504-428">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3f504-429">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-429">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="3f504-430">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-430">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="3f504-431">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-431">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="3f504-432">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-432">Edit the *web.config* file.</span></span> <span data-ttu-id="3f504-433">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-433">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="3f504-434">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="3f504-434">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="3f504-435">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-435">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="3f504-436">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="3f504-436">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="3f504-437">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-437">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="3f504-438">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-438">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="3f504-439">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-439">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-440">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-440">Navigate to the *logs* folder.</span></span> <span data-ttu-id="3f504-441">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-441">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="3f504-442">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-442">Study the log for errors.</span></span>

<span data-ttu-id="3f504-443">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-443">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="3f504-444">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-444">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-445">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-445">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="3f504-446">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-446">Save the file.</span></span>

<span data-ttu-id="3f504-447">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-447">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-448">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-448">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-449">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-449">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="3f504-450">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-450">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-451">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-451">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="3f504-452">ASP.NET Core モジュールのデバッグ ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-452">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="3f504-453">ASP.NET Core モジュールのデバッグ ログを有効にするには、次のハンドラー設定をアプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="3f504-453">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="3f504-454">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-454">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="3f504-455">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-455">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="3f504-456">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="3f504-456">Enable the Developer Exception Page</span></span>

<span data-ttu-id="3f504-457">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="3f504-457">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="3f504-458">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-458">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="3f504-459">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-459">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="3f504-460">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-460">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="3f504-461">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-461">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="3f504-462">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="3f504-462">Obtain data from an app</span></span>

<span data-ttu-id="3f504-463">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="3f504-463">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="3f504-464">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-464">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="3f504-465">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-465">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="3f504-466">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3f504-466">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="3f504-467">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="3f504-467">App crashes or encounters an exception</span></span>

<span data-ttu-id="3f504-468">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="3f504-468">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="3f504-469">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-469">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="3f504-470">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="3f504-470">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="3f504-471">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-471">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="3f504-472">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-472">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="3f504-473">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-473">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="3f504-474">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-474">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="3f504-475">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-475">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="3f504-476">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-476">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="3f504-477">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-477">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="3f504-478">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="3f504-478">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="3f504-479">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-479">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-480">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-480">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="3f504-481">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="3f504-481">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="3f504-482">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-482">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="3f504-483">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="3f504-483">Analyze the dump</span></span>

<span data-ttu-id="3f504-484">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-484">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="3f504-485">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-485">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="3f504-486">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="3f504-486">Clear package caches</span></span>

<span data-ttu-id="3f504-487">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-487">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="3f504-488">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-488">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="3f504-489">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-489">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="3f504-490">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-490">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="3f504-491">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="3f504-491">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="3f504-492">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-492">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="3f504-493">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-493">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="3f504-494">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="3f504-494">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="3f504-495">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-495">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f504-496">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3f504-496">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="3f504-497">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-497">Azure documentation</span></span>

* [<span data-ttu-id="3f504-498">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="3f504-498">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="3f504-499">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="3f504-499">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="3f504-500">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="3f504-500">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="3f504-501">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="3f504-501">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="3f504-502">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-502">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="3f504-503">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-503">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="3f504-504">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-504">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="3f504-505">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="3f504-505">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="3f504-506">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="3f504-506">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="3f504-507">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="3f504-507">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="3f504-508">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-508">Visual Studio documentation</span></span>

* [<span data-ttu-id="3f504-509">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-509">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="3f504-510">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-510">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="3f504-511">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="3f504-511">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="3f504-512">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-512">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="3f504-513">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-513">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3f504-514">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-514">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="3f504-515">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-515">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="3f504-516">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3f504-516">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="3f504-517">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-517">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="3f504-518">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-518">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="3f504-519">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-519">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="3f504-520">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-520">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="3f504-521">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-521">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="3f504-522">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="3f504-522">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="3f504-523">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3f504-523">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="3f504-524">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3f504-524">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="3f504-525">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-525">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="3f504-526">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-526">App startup errors</span></span>

<span data-ttu-id="3f504-527">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="3f504-527">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="3f504-528">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 - 処理エラー*" または "*500.30 - 開始エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-528">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="3f504-529">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="3f504-529">403.14 Forbidden</span></span>

<span data-ttu-id="3f504-530">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="3f504-530">The app fails to start.</span></span> <span data-ttu-id="3f504-531">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-531">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="3f504-532">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-532">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="3f504-533">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="3f504-533">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="3f504-534">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="3f504-534">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="3f504-535">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-535">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="3f504-536">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="3f504-536">Perform the following steps:</span></span>

1. <span data-ttu-id="3f504-537">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-537">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="3f504-538">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="3f504-538">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="3f504-539">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-539">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="3f504-540">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-540">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="3f504-541">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-541">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="3f504-542">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-542">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="3f504-543">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-543">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="3f504-544">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-544">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="3f504-545">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="3f504-545">500 Internal Server Error</span></span>

<span data-ttu-id="3f504-546">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="3f504-546">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="3f504-547">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-547">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="3f504-548">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-548">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="3f504-549">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-549">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="3f504-550">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="3f504-550">From the server's perspective, that's correct.</span></span> <span data-ttu-id="3f504-551">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="3f504-551">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="3f504-552">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-552">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="3f504-553">500.0 インプロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-553">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="3f504-554">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-554">The worker process fails.</span></span> <span data-ttu-id="3f504-555">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-555">The app doesn't start.</span></span>

<span data-ttu-id="3f504-556">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は .NET Core CLR の検出に失敗し、インプロセス要求ハンドラー (*aspnetcorev2_inprocess.dll*) を検出します。</span><span class="sxs-lookup"><span data-stu-id="3f504-556">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="3f504-557">次の点をご確認ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-557">Check that:</span></span>

* <span data-ttu-id="3f504-558">アプリが [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet パッケージまたは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)を対象としている。</span><span class="sxs-lookup"><span data-stu-id="3f504-558">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="3f504-559">アプリが対象としているバージョンの ASP.NET Core 共有フレームワークが対象のコンピューターにインストールされている。</span><span class="sxs-lookup"><span data-stu-id="3f504-559">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="3f504-560">500.0 アウト プロセス ハンドラーの読み込みエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-560">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="3f504-561">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-561">The worker process fails.</span></span> <span data-ttu-id="3f504-562">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-562">The app doesn't start.</span></span>

<span data-ttu-id="3f504-563">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)は、アウト プロセスのホスティング要求ハンドラーの検索に失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-563">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="3f504-564">*aspnetcorev2_outofprocess.dll* が *aspnetcorev2.dll* の隣のサブフォルダーにあることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-564">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="3f504-565">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="3f504-565">502.5 Process Failure</span></span>

<span data-ttu-id="3f504-566">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-566">The worker process fails.</span></span> <span data-ttu-id="3f504-567">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-567">The app doesn't start.</span></span>

<span data-ttu-id="3f504-568">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-568">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="3f504-569">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-569">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="3f504-570">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="3f504-570">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="3f504-571">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-571">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="3f504-572">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-572">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="3f504-573">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-573">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="3f504-574">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-574">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="3f504-575">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-575">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="3f504-576">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="3f504-576">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="3f504-577">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="3f504-577">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="3f504-578">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-578">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="3f504-579">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-579">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="3f504-580">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-580">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="3f504-581">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-581">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="3f504-582">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-582">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="3f504-583">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-583">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="3f504-584">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-584">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="3f504-585">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-585">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="3f504-586">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="3f504-586">Connection reset</span></span>

<span data-ttu-id="3f504-587">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="3f504-587">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="3f504-588">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-588">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="3f504-589">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-589">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="3f504-590">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-590">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="3f504-591">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="3f504-591">Default startup limits</span></span>

<span data-ttu-id="3f504-592">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-592">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="3f504-593">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="3f504-593">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="3f504-594">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-594">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="3f504-595">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-595">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="3f504-596">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-596">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="3f504-597">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-597">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="3f504-598">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-598">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="3f504-599">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-599">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="3f504-600">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-600">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="3f504-601">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-601">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="3f504-602">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-602">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="3f504-603">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="3f504-603">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="3f504-604">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-604">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-605">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-605">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-606">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-606">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-607">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-607">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-608">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-608">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="3f504-609">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-609">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="3f504-610">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-610">Examine the log.</span></span> <span data-ttu-id="3f504-611">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-611">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="3f504-612">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-612">Run the app in the Kudu console</span></span>

<span data-ttu-id="3f504-613">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="3f504-613">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="3f504-614">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="3f504-614">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="3f504-615">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-615">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-616">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-616">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-617">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-617">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-618">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-618">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="3f504-619">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3f504-619">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="3f504-620">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="3f504-620">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="3f504-621">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-621">Run the app:</span></span>
   * <span data-ttu-id="3f504-622">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-622">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="3f504-623">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-623">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="3f504-624">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-624">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="3f504-625">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="3f504-625">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="3f504-626">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="3f504-626">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="3f504-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="3f504-627">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="3f504-628">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-628">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="3f504-629">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-629">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="3f504-630">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3f504-630">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="3f504-631">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="3f504-631">**Current release**</span></span>

* <span data-ttu-id="3f504-632">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-632">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="3f504-633">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-633">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="3f504-634">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-634">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="3f504-635">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="3f504-635">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="3f504-636">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-636">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="3f504-637">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="3f504-637">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="3f504-638">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="3f504-638">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="3f504-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="3f504-639">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="3f504-640">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-640">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="3f504-641">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-641">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="3f504-642">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-642">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="3f504-643">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-643">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="3f504-644">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3f504-644">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3f504-645">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-645">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="3f504-646">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-646">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="3f504-647">**[推奨されている解決方法]** > **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、 **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-647">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="3f504-648">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-648">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3f504-649">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-649">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="3f504-650">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="3f504-650">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-651">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-651">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="3f504-652">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-652">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="3f504-653">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-653">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-654">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="3f504-654">Return to the Azure portal.</span></span> <span data-ttu-id="3f504-655">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-655">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="3f504-656">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-656">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-657">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-657">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-658">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-658">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-659">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-659">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="3f504-660">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-660">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="3f504-661">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-661">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="3f504-662">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-662">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="3f504-663">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-663">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="3f504-664">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-664">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="3f504-665">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-665">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="3f504-666">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-666">Select **Save** to save the file.</span></span>

<span data-ttu-id="3f504-667">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-667">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-668">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-668">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-669">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-669">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="3f504-670">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="3f504-670">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="3f504-671">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-671">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-672">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-672">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a><span data-ttu-id="3f504-673">ASP.NET Core モジュールのデバッグ ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-673">ASP.NET Core Module debug log (Azure App Service)</span></span>

<span data-ttu-id="3f504-674">ASP.NET Core モジュール デバッグ ログでは、ASP.NET Core モジュールのさらに詳しいログが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-674">The ASP.NET Core Module debug log provides additional, deeper logging from the ASP.NET Core Module.</span></span> <span data-ttu-id="3f504-675">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3f504-675">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3f504-676">強化された診断ログを有効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-676">To enable the enhanced diagnostic log, perform either of the following:</span></span>
   * <span data-ttu-id="3f504-677">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」の指示に従い、強化された診断ログをアプリに対して設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-677">Follow the instructions in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to configure the app for an enhanced diagnostic logging.</span></span> <span data-ttu-id="3f504-678">アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="3f504-678">Redeploy the app.</span></span>
   * <span data-ttu-id="3f504-679">Kudu コンソールを利用し、「`<handlerSettings>`強化された診断ログ[」にある ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) をライブ アプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="3f504-679">Add the `<handlerSettings>` shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) to the live app's *web.config* file using the Kudu console:</span></span>
     1. <span data-ttu-id="3f504-680">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-680">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-681">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-681">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-682">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-682">The Kudu console opens in a new browser tab or window.</span></span>
     1. <span data-ttu-id="3f504-683">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-683">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
     1. <span data-ttu-id="3f504-684">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-684">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3f504-685">鉛筆アイコンを選択し、*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-685">Edit the *web.config* file by selecting the pencil button.</span></span> <span data-ttu-id="3f504-686">「[強化された診断ログ](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)」にある `<handlerSettings>` セクションを追加します。</span><span class="sxs-lookup"><span data-stu-id="3f504-686">Add the `<handlerSettings>` section as shown in [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="3f504-687">**[保存]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-687">Select the **Save** button.</span></span>
1. <span data-ttu-id="3f504-688">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-688">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-689">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-689">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-690">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-690">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-691">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-691">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-692">パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-692">Open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3f504-693">*aspnetcore-debug.log* ファイルにパスを指定しなかった場合、ファイルが一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-693">If you didn't supply a path for the *aspnetcore-debug.log* file, the file appears in the list.</span></span> <span data-ttu-id="3f504-694">パスを指定した場合、ログ ファイルの場所に移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-694">If you supplied a path, navigate to the location of the log file.</span></span>
1. <span data-ttu-id="3f504-695">ファイル名の隣にある鉛筆アイコンでログ ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-695">Open the log file with the pencil button next to the file name.</span></span>

<span data-ttu-id="3f504-696">トラブルシューティングが完了したら、debug ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-696">Disable debug logging when troubleshooting is complete:</span></span>

<span data-ttu-id="3f504-697">強化されたデバッグ ログを無効にするには、次のいずれかを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-697">To disable the enhanced debug log, perform either of the following:</span></span>

* <span data-ttu-id="3f504-698">ローカルの *web.config* ファイルから `<handlerSettings>` を削除し、アプリを再デプロイします。</span><span class="sxs-lookup"><span data-stu-id="3f504-698">Remove the `<handlerSettings>` from the *web.config* file locally and redeploy the app.</span></span>
* <span data-ttu-id="3f504-699">Kudu コンソールを使用して *web.config* ファイルを編集し、`<handlerSettings>` セクションを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-699">Use the Kudu console to edit the *web.config* file and remove the `<handlerSettings>` section.</span></span> <span data-ttu-id="3f504-700">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-700">Save the file.</span></span>

<span data-ttu-id="3f504-701">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-701">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-702">debug ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-702">Failure to disable the debug log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-703">ログ ファイルのサイズに制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-703">There's no limit on log file size.</span></span> <span data-ttu-id="3f504-704">debug ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="3f504-704">Only use debug logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="3f504-705">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-705">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-706">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-706">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="3f504-707">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-707">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="3f504-708">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-708">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="3f504-709">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-709">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="3f504-710">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="3f504-710">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="3f504-711">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="3f504-711">Monitoring blades</span></span>

<span data-ttu-id="3f504-712">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-712">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="3f504-713">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-713">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="3f504-714">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-714">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="3f504-715">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="3f504-715">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="3f504-716">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-716">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="3f504-717">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-717">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="3f504-718">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-718">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="3f504-719">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-719">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="3f504-720">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="3f504-720">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="3f504-721">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-721">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="3f504-722">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-722">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="3f504-723">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="3f504-723">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="3f504-724">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-724">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="3f504-725">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-725">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-726">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-726">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-727">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-727">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-728">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-728">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="3f504-729">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="3f504-729">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-730">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-730">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="3f504-731">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-731">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="3f504-732">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="3f504-732">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="3f504-733">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-733">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="3f504-734">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-734">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="3f504-735">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-735">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="3f504-736">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-736">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="3f504-737">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-737">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="3f504-738">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-738">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-739">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-739">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="3f504-740">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="3f504-740">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="3f504-741">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="3f504-741">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="3f504-742">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-742">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="3f504-743">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-743">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="3f504-744">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-744">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="3f504-745">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-745">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-746">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-746">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-747">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-747">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="3f504-748">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-748">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-749">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-749">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="3f504-750">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-750">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="3f504-751">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-751">Application Event Log (IIS)</span></span>

<span data-ttu-id="3f504-752">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="3f504-752">Access the Application Event Log:</span></span>

1. <span data-ttu-id="3f504-753">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-753">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="3f504-754">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-754">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="3f504-755">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-755">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="3f504-756">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="3f504-756">Search for errors associated with the failing app.</span></span> <span data-ttu-id="3f504-757">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-757">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="3f504-758">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="3f504-758">Run the app at a command prompt</span></span>

<span data-ttu-id="3f504-759">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="3f504-759">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="3f504-760">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-760">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="3f504-761">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="3f504-761">Framework-dependent deployment</span></span>

<span data-ttu-id="3f504-762">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-762">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="3f504-763">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-763">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="3f504-764">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-764">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="3f504-765">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-765">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="3f504-766">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-766">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="3f504-767">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="3f504-767">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="3f504-768">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="3f504-768">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="3f504-769">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="3f504-769">Self-contained deployment</span></span>

<span data-ttu-id="3f504-770">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-770">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="3f504-771">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-771">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="3f504-772">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-772">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="3f504-773">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-773">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="3f504-774">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-774">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="3f504-775">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="3f504-775">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="3f504-776">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="3f504-776">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="3f504-777">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-777">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="3f504-778">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3f504-778">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3f504-779">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-779">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="3f504-780">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-780">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="3f504-781">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-781">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="3f504-782">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-782">Edit the *web.config* file.</span></span> <span data-ttu-id="3f504-783">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-783">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="3f504-784">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="3f504-784">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="3f504-785">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-785">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="3f504-786">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="3f504-786">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="3f504-787">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-787">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="3f504-788">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-788">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="3f504-789">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-789">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-790">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-790">Navigate to the *logs* folder.</span></span> <span data-ttu-id="3f504-791">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-791">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="3f504-792">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-792">Study the log for errors.</span></span>

<span data-ttu-id="3f504-793">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-793">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="3f504-794">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-794">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-795">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-795">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="3f504-796">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-796">Save the file.</span></span>

<span data-ttu-id="3f504-797">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-797">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-798">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-798">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-799">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-799">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="3f504-800">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-800">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-801">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-801">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="aspnet-core-module-debug-log-iis"></a><span data-ttu-id="3f504-802">ASP.NET Core モジュールのデバッグ ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-802">ASP.NET Core Module debug log (IIS)</span></span>

<span data-ttu-id="3f504-803">ASP.NET Core モジュールのデバッグ ログを有効にするには、次のハンドラー設定をアプリの *web.config* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="3f504-803">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug log:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="3f504-804">ログに指定されたパスが存在することと、アプリ プールの ID にその場所への書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-804">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

<span data-ttu-id="3f504-805">詳細については、「<xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-805">For more information, see <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="3f504-806">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="3f504-806">Enable the Developer Exception Page</span></span>

<span data-ttu-id="3f504-807">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="3f504-807">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="3f504-808">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-808">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="3f504-809">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-809">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="3f504-810">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-810">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="3f504-811">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-811">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="3f504-812">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="3f504-812">Obtain data from an app</span></span>

<span data-ttu-id="3f504-813">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="3f504-813">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="3f504-814">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-814">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="3f504-815">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-815">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="3f504-816">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3f504-816">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="3f504-817">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="3f504-817">App crashes or encounters an exception</span></span>

<span data-ttu-id="3f504-818">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="3f504-818">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="3f504-819">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-819">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="3f504-820">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="3f504-820">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="3f504-821">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-821">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="3f504-822">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-822">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="3f504-823">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-823">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="3f504-824">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-824">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="3f504-825">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-825">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="3f504-826">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-826">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="3f504-827">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-827">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="3f504-828">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="3f504-828">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="3f504-829">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-829">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-830">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-830">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="3f504-831">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="3f504-831">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="3f504-832">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-832">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="3f504-833">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="3f504-833">Analyze the dump</span></span>

<span data-ttu-id="3f504-834">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-834">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="3f504-835">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-835">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="3f504-836">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="3f504-836">Clear package caches</span></span>

<span data-ttu-id="3f504-837">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-837">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="3f504-838">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-838">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="3f504-839">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-839">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="3f504-840">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-840">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="3f504-841">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="3f504-841">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="3f504-842">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-842">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="3f504-843">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-843">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="3f504-844">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="3f504-844">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="3f504-845">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-845">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f504-846">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3f504-846">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="3f504-847">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-847">Azure documentation</span></span>

* [<span data-ttu-id="3f504-848">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="3f504-848">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="3f504-849">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="3f504-849">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="3f504-850">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="3f504-850">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="3f504-851">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="3f504-851">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="3f504-852">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-852">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="3f504-853">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-853">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="3f504-854">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-854">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="3f504-855">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="3f504-855">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="3f504-856">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="3f504-856">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="3f504-857">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="3f504-857">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="3f504-858">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-858">Visual Studio documentation</span></span>

* [<span data-ttu-id="3f504-859">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-859">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="3f504-860">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-860">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="3f504-861">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="3f504-861">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="3f504-862">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-862">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="3f504-863">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-863">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="3f504-864">この記事では、アプリの起動時に発生する一般的なエラーに関する情報と、アプリが Azure App Service または IIS にデプロイされるときのエラーを診断する手順を提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-864">This article provides information on common app startup errors and instructions on how to diagnose errors when an app is deployed to Azure App Service or IIS:</span></span>

[<span data-ttu-id="3f504-865">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-865">App startup errors</span></span>](#app-startup-errors)  
<span data-ttu-id="3f504-866">一般的な起動時の HTTP 状態コードのシナリオについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3f504-866">Explains common startup HTTP status code scenarios.</span></span>

[<span data-ttu-id="3f504-867">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-867">Troubleshoot on Azure App Service</span></span>](#troubleshoot-on-azure-app-service)  
<span data-ttu-id="3f504-868">Azure App Service にデプロイされたアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-868">Provides troubleshooting advice for apps deployed to Azure App Service.</span></span>

[<span data-ttu-id="3f504-869">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-869">Troubleshoot on IIS</span></span>](#troubleshoot-on-iis)  
<span data-ttu-id="3f504-870">IIS にデプロイされているアプリまたは IIS Express でローカルに実行されているアプリに関するトラブルシューティングのアドバイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3f504-870">Provides troubleshooting advice for apps deployed to IIS or running on IIS Express locally.</span></span> <span data-ttu-id="3f504-871">このガイダンスは、Windows Server と Windows デスクトップの両方の配置に適用されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-871">The guidance applies to both Windows Server and Windows desktop deployments.</span></span>

[<span data-ttu-id="3f504-872">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="3f504-872">Clear package caches</span></span>](#clear-package-caches)  
<span data-ttu-id="3f504-873">統一性のないパッケージにより、メジャー アップグレードの実行時またはパッケージ バージョンの変更時に、アプリが中断したときの対処方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="3f504-873">Explains what to do when incoherent packages break an app when performing major upgrades or changing package versions.</span></span>

[<span data-ttu-id="3f504-874">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="3f504-874">Additional resources</span></span>](#additional-resources)  
<span data-ttu-id="3f504-875">その他のトラブルシューティングのトピックを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-875">Lists additional troubleshooting topics.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="3f504-876">アプリ起動時のエラー</span><span class="sxs-lookup"><span data-stu-id="3f504-876">App startup errors</span></span>

<span data-ttu-id="3f504-877">Visual Studio では、ASP.NET Core プロジェクトのデバッグ時に [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) のホスティングが既定の設定です。</span><span class="sxs-lookup"><span data-stu-id="3f504-877">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="3f504-878">このトピックのアドバイスを使用すると、ローカルでのデバッグ時に発生する "*502.5 処理エラー*" を診断できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-878">A *502.5 Process Failure* that occurs when debugging locally can be diagnosed using the advice in this topic.</span></span>

### <a name="40314-forbidden"></a><span data-ttu-id="3f504-879">403.14 禁止</span><span class="sxs-lookup"><span data-stu-id="3f504-879">403.14 Forbidden</span></span>

<span data-ttu-id="3f504-880">アプリの開始に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="3f504-880">The app fails to start.</span></span> <span data-ttu-id="3f504-881">次のエラーがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-881">The following error is logged:</span></span>

```
The Web server is configured to not list the contents of this directory.
```

<span data-ttu-id="3f504-882">このエラーが発生するのは、通常、ホスト システム上の配置が壊れている場合です。これには、次のようなシナリオが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-882">The error is usually caused by a broken deployment on the hosting system, which includes any of the following scenarios:</span></span>

* <span data-ttu-id="3f504-883">アプリがホスト システム上の不適切なフォルダーに配置されています。</span><span class="sxs-lookup"><span data-stu-id="3f504-883">The app is deployed to the wrong folder on the hosting system.</span></span>
* <span data-ttu-id="3f504-884">配置プロセスで、アプリのすべてのファイルとフォルダーを、ホスティング システムの配置フォルダーに移動できませんでした。</span><span class="sxs-lookup"><span data-stu-id="3f504-884">The deployment process failed to move all of the app's files and folders to the deployment folder on the hosting system.</span></span>
* <span data-ttu-id="3f504-885">配置に *web.config* ファイルがありません。または、*web.config* ファイルの内容の形式が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-885">The *web.config* file is missing from the deployment, or the *web.config* file contents are malformed.</span></span>

<span data-ttu-id="3f504-886">次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="3f504-886">Perform the following steps:</span></span>

1. <span data-ttu-id="3f504-887">ホスト システム上の配置フォルダーからすべてのファイルとフォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-887">Delete all of the files and folders from the deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="3f504-888">Visual Studio、PowerShell、手動配置など、通常の配置方法を使用して、アプリの "*公開*" フォルダーの内容をホスティング システムに再配置します。</span><span class="sxs-lookup"><span data-stu-id="3f504-888">Redeploy the contents of the app's *publish* folder to the hosting system using your normal method of deployment, such as Visual Studio, PowerShell, or manual deployment:</span></span>
   * <span data-ttu-id="3f504-889">*web.config* ファイルがそのデプロイに存在し、その内容が正しいことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-889">Confirm that the *web.config* file is present in the deployment and that its contents are correct.</span></span>
   * <span data-ttu-id="3f504-890">Azure App Service でホストする場合は、アプリが `D:\home\site\wwwroot` フォルダーにデプロイされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-890">When hosting on Azure App Service, confirm that the app is deployed to the `D:\home\site\wwwroot` folder.</span></span>
   * <span data-ttu-id="3f504-891">アプリが IIS によってホストされている場合は、アプリが **IIS マネージャー**の **[基本設定]** に表示されている IIS の**物理パス**に配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-891">When the app is hosted by IIS, confirm that the app is deployed to the IIS **Physical path** shown in **IIS Manager**'s **Basic Settings**.</span></span>
1. <span data-ttu-id="3f504-892">ホスティング システムの配置をプロジェクトの "*公開*" フォルダーの内容と比較して、アプリのすべてのファイルとフォルダーが配置されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-892">Confirm that all of the app's files and folders are deployed by comparing the deployment on the hosting system to the contents of the project's *publish* folder.</span></span>

<span data-ttu-id="3f504-893">公開された ASP.NET Core アプリのレイアウトの詳細については、「<xref:host-and-deploy/directory-structure>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-893">For more information on the layout of a published ASP.NET Core app, see <xref:host-and-deploy/directory-structure>.</span></span> <span data-ttu-id="3f504-894">*web.config* ファイルの詳細については、「<xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-894">For more information on the *web.config* file, see <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="3f504-895">500 内部サーバー エラー</span><span class="sxs-lookup"><span data-stu-id="3f504-895">500 Internal Server Error</span></span>

<span data-ttu-id="3f504-896">アプリは起動しますが、エラーのためにサーバーは要求を実行できません。</span><span class="sxs-lookup"><span data-stu-id="3f504-896">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="3f504-897">このエラーは、起動時または応答の作成中に、アプリのコード内で発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-897">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="3f504-898">応答にコンテンツが含まれていないか、またはブラウザーに "*500 内部サーバー エラー*" という応答が表示される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-898">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="3f504-899">通常、アプリケーション イベント ログではアプリが正常に起動したことが示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-899">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="3f504-900">サーバーから見るとそれは正しいことです。</span><span class="sxs-lookup"><span data-stu-id="3f504-900">From the server's perspective, that's correct.</span></span> <span data-ttu-id="3f504-901">アプリは起動しましたが、有効な応答を生成できません。</span><span class="sxs-lookup"><span data-stu-id="3f504-901">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="3f504-902">問題を解決するには、サーバー上のコマンド プロンプトでアプリを実行するか、ASP.NET Core モジュールの stdout ログを有効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-902">Run the app at a command prompt on the server or enable the ASP.NET Core Module stdout log to troubleshoot the problem.</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="3f504-903">502.5 処理エラー</span><span class="sxs-lookup"><span data-stu-id="3f504-903">502.5 Process Failure</span></span>

<span data-ttu-id="3f504-904">ワーカー プロセスが失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-904">The worker process fails.</span></span> <span data-ttu-id="3f504-905">アプリは起動しません。</span><span class="sxs-lookup"><span data-stu-id="3f504-905">The app doesn't start.</span></span>

<span data-ttu-id="3f504-906">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)はワーカー プロセスの開始を試みますが、開始に失敗します。</span><span class="sxs-lookup"><span data-stu-id="3f504-906">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="3f504-907">プロセス起動時のエラーの原因は、通常、アプリケーション イベント ログと ASP.NET Core モジュールの stdout ログのエントリから判断できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-907">The cause of a process startup failure can usually be determined from entries in the Application Event Log and the ASP.NET Core Module stdout log.</span></span>

<span data-ttu-id="3f504-908">一般的なエラー条件は、存在しないバージョンの ASP.NET Core 共有フレームワークが対象にされていて、アプリが正しく構成されていないことです。</span><span class="sxs-lookup"><span data-stu-id="3f504-908">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="3f504-909">対象のコンピューターにどのバージョンの ASP.NET Core 共有フレームワークがインストールされているかを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-909">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="3f504-910">*共有フレームワーク*は、コンピューター上にインストールされたアセンブリ ( *.dll* ファイル) のセットであり、`Microsoft.AspNetCore.App` などのメタパッケージによって参照されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-910">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="3f504-911">メタパッケージの参照には、必要な最低限のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-911">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="3f504-912">詳しくは、[共有フレームワーク](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-912">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="3f504-913">正しく構成されていないホスティングやアプリが原因でワーカー プロセスが失敗する場合、"*502.5 処理エラー*" のエラー ページが返されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-913">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="3f504-914">アプリケーションの起動に失敗しました (ErrorCode '0x800700c1')</span><span class="sxs-lookup"><span data-stu-id="3f504-914">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="3f504-915">アプリのアセンブリ ( *.dll*) を読み込めなかったため、アプリの起動に失敗しました。</span><span class="sxs-lookup"><span data-stu-id="3f504-915">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="3f504-916">このエラーは、公開されたアプリと w3wp/iisexpress プロセスの間でビットの不一致がある場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-916">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="3f504-917">アプリ プールの 32 ビット設定が正しいことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-917">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="3f504-918">IIS マネージャーの **[アプリケーション プール]** でそのアプリ プールを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-918">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="3f504-919">**[アクション]** パネルの **[アプリケーション プールの編集]** で **[詳細設定]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-919">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="3f504-920">**[32 ビット アプリケーションの有効化]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-920">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="3f504-921">32 ビット (x86) アプリを展開する場合は、この値を `True` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-921">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="3f504-922">64 ビット (x64) アプリを展開する場合は、この値を `False` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-922">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

<span data-ttu-id="3f504-923">プロジェクト ファイルの `<Platform>` MSBuild プロパティと公開されたアプリのビット数との間で競合が発生していないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-923">Confirm that there isn't a conflict between a `<Platform>` MSBuild property in the project file and the published bitness of the app.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="3f504-924">接続のリセット</span><span class="sxs-lookup"><span data-stu-id="3f504-924">Connection reset</span></span>

<span data-ttu-id="3f504-925">ヘッダー送信後にエラーが発生した場合、サーバーが **500 内部サーバー エラー**を送信するには遅すぎます。</span><span class="sxs-lookup"><span data-stu-id="3f504-925">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="3f504-926">このような状況は、応答に対する複雑なオブジェクトのシリアル化中にエラーが起きたときによく発生します。</span><span class="sxs-lookup"><span data-stu-id="3f504-926">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="3f504-927">この種のエラーは、クライアントでは "*接続リセット*" エラーとして表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-927">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="3f504-928">この種のエラーのトラブルシューティングには、[アプリケーション ログ](xref:fundamentals/logging/index)が役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-928">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

### <a name="default-startup-limits"></a><span data-ttu-id="3f504-929">既定の起動制限</span><span class="sxs-lookup"><span data-stu-id="3f504-929">Default startup limits</span></span>

<span data-ttu-id="3f504-930">[ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)の *startupTimeLimit* は、既定では 120 秒に構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-930">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="3f504-931">既定値のままにした場合、モジュールで処理エラーが記録されるまでに、アプリは最大で 2 分を起動にかけることができます。</span><span class="sxs-lookup"><span data-stu-id="3f504-931">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="3f504-932">モジュールの構成の詳細については、「[AspNetCore 要素の属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-932">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-on-azure-app-service"></a><span data-ttu-id="3f504-933">Azure App Service でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-933">Troubleshoot on Azure App Service</span></span>

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a><span data-ttu-id="3f504-934">アプリケーション イベント ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-934">Application Event Log (Azure App Service)</span></span>

<span data-ttu-id="3f504-935">アプリケーション イベント ログにアクセスするには、Azure portal の **[問題の診断と解決]** ブレードを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-935">To access the Application Event Log, use the **Diagnose and solve problems** blade in the Azure portal:</span></span>

1. <span data-ttu-id="3f504-936">Azure portal の **[App Services]** でアプリを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-936">In the Azure portal, open the app in **App Services**.</span></span>
1. <span data-ttu-id="3f504-937">**[問題の診断と解決]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-937">Select **Diagnose and solve problems**.</span></span>
1. <span data-ttu-id="3f504-938">**[診断ツール]** という見出しを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-938">Select the **Diagnostic Tools** heading.</span></span>
1. <span data-ttu-id="3f504-939">**[サポート ツール]** で **[アプリケーション イベント]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-939">Under **Support Tools**, select the **Application Events** button.</span></span>
1. <span data-ttu-id="3f504-940">**[Source]\(ソース\)** 列で、*IIS AspNetCoreModule* または *IIS AspNetCoreModule V2* によって提供された最新のエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-940">Examine the latest error provided by the *IIS AspNetCoreModule* or *IIS AspNetCoreModule V2* entry in the **Source** column.</span></span>

<span data-ttu-id="3f504-941">**[問題の診断と解決]** ブレードを使う代わりに、[Kudu](https://github.com/projectkudu/kudu/wiki) を使ってアプリケーション イベント ログ ファイルを直接調べることもできます。</span><span class="sxs-lookup"><span data-stu-id="3f504-941">An alternative to using the **Diagnose and solve problems** blade is to examine the Application Event Log file directly using [Kudu](https://github.com/projectkudu/kudu/wiki):</span></span>

1. <span data-ttu-id="3f504-942">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-942">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-943">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-943">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-944">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-944">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-945">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-945">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-946">**LogFiles** フォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-946">Open the **LogFiles** folder.</span></span>
1. <span data-ttu-id="3f504-947">*eventlog.xml* ファイルの横にある鉛筆アイコンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-947">Select the pencil icon next to the *eventlog.xml* file.</span></span>
1. <span data-ttu-id="3f504-948">ログを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-948">Examine the log.</span></span> <span data-ttu-id="3f504-949">ログの末尾までスクロールし、最新のイベントを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-949">Scroll to the bottom of the log to see the most recent events.</span></span>

### <a name="run-the-app-in-the-kudu-console"></a><span data-ttu-id="3f504-950">Kudu コンソールでアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-950">Run the app in the Kudu console</span></span>

<span data-ttu-id="3f504-951">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="3f504-951">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="3f504-952">[Kudu](https://github.com/projectkudu/kudu/wiki) のリモート実行コンソールでアプリを実行すると、エラーを検出することができます。</span><span class="sxs-lookup"><span data-stu-id="3f504-952">You can run the app in the [Kudu](https://github.com/projectkudu/kudu/wiki) Remote Execution Console to discover the error:</span></span>

1. <span data-ttu-id="3f504-953">**[開発ツール]** 領域で **[高度なツール]** を開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-953">Open **Advanced Tools** in the **Development Tools** area.</span></span> <span data-ttu-id="3f504-954">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-954">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-955">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-955">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-956">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-956">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>

#### <a name="test-a-32-bit-x86-app"></a><span data-ttu-id="3f504-957">32 ビット (x86) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3f504-957">Test a 32-bit (x86) app</span></span>

<span data-ttu-id="3f504-958">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="3f504-958">**Current release**</span></span>

1. `cd d:\home\site\wwwroot`
1. <span data-ttu-id="3f504-959">次のようにアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-959">Run the app:</span></span>
   * <span data-ttu-id="3f504-960">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-960">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * <span data-ttu-id="3f504-961">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-961">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

     ```console
     {ASSEMBLY NAME}.exe
     ```

<span data-ttu-id="3f504-962">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-962">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="3f504-963">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="3f504-963">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="3f504-964">"*ASP.NET Core {バージョン} (x86) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="3f504-964">*Requires installing the ASP.NET Core {VERSION} (x86) Runtime site extension.*</span></span>

1. <span data-ttu-id="3f504-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="3f504-965">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="3f504-966">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-966">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="3f504-967">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-967">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

#### <a name="test-a-64-bit-x64-app"></a><span data-ttu-id="3f504-968">64 ビット (x64) アプリをテストする</span><span class="sxs-lookup"><span data-stu-id="3f504-968">Test a 64-bit (x64) app</span></span>

<span data-ttu-id="3f504-969">**現在のリリース**</span><span class="sxs-lookup"><span data-stu-id="3f504-969">**Current release**</span></span>

* <span data-ttu-id="3f504-970">アプリが 64 ビット (x64) の[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-970">If the app is a 64-bit (x64) [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>
  1. `cd D:\Program Files\dotnet`
  1. <span data-ttu-id="3f504-971">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-971">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>
* <span data-ttu-id="3f504-972">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-972">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>
  1. `cd D:\home\site\wwwroot`
  1. <span data-ttu-id="3f504-973">アプリを実行します: `{ASSEMBLY NAME}.exe`</span><span class="sxs-lookup"><span data-stu-id="3f504-973">Run the app: `{ASSEMBLY NAME}.exe`</span></span>

<span data-ttu-id="3f504-974">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-974">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

<span data-ttu-id="3f504-975">**プレビュー リリース上で実行されているフレームワークに依存するデプロイ**</span><span class="sxs-lookup"><span data-stu-id="3f504-975">**Framework-dependent deployment running on a preview release**</span></span>

<span data-ttu-id="3f504-976">"*ASP.NET Core {バージョン} (x64) ランタイムのサイト拡張機能をインストールする必要があります。* "</span><span class="sxs-lookup"><span data-stu-id="3f504-976">*Requires installing the ASP.NET Core {VERSION} (x64) Runtime site extension.*</span></span>

1. <span data-ttu-id="3f504-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` はランタイム バージョンです)</span><span class="sxs-lookup"><span data-stu-id="3f504-977">`cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` is the runtime version)</span></span>
1. <span data-ttu-id="3f504-978">アプリを実行します: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span><span class="sxs-lookup"><span data-stu-id="3f504-978">Run the app: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`</span></span>

<span data-ttu-id="3f504-979">エラーを示すアプリからのコンソール出力はすべて、Kudu コンソールにパイプされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-979">The console output from the app, showing any errors, is piped to the Kudu console.</span></span>

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a><span data-ttu-id="3f504-980">ASP.NET Core モジュールの stdout ログ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-980">ASP.NET Core Module stdout log (Azure App Service)</span></span>

<span data-ttu-id="3f504-981">ASP.NET Core モジュールの stdout には、アプリケーション イベント ログでは見つからない有用なエラー メッセージが記録されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-981">The ASP.NET Core Module stdout log often records useful error messages not found in the Application Event Log.</span></span> <span data-ttu-id="3f504-982">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3f504-982">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3f504-983">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-983">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="3f504-984">**[SELECT PROBLEM CATEGORY]\(問題カテゴリの選択\)** で、 **[Web App Down]\(Web アプリのダウン\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-984">Under **SELECT PROBLEM CATEGORY**, select the **Web App Down** button.</span></span>
1. <span data-ttu-id="3f504-985">**[推奨されている解決方法]** > **[Enable Stdout Log Redirection]\(Stdout ログのリダイレクトを有効にする\)** で、 **[Open Kudu Console to edit Web.Config]\(Kudu コンソールを開いて Web.Config を編集する\)** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-985">Under **Suggested Solutions** > **Enable Stdout Log Redirection**, select the button to **Open Kudu Console to edit Web.Config**.</span></span>
1. <span data-ttu-id="3f504-986">Kudu の**診断コンソール**で、パス **site** > **wwwroot** へのフォルダーを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-986">In the Kudu **Diagnostic Console**, open the folders to the path **site** > **wwwroot**.</span></span> <span data-ttu-id="3f504-987">下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-987">Scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="3f504-988">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="3f504-988">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-989">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-989">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="3f504-990">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-990">Select **Save** to save the updated *web.config* file.</span></span>
1. <span data-ttu-id="3f504-991">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-991">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-992">Azure portal に戻ります。</span><span class="sxs-lookup"><span data-stu-id="3f504-992">Return to the Azure portal.</span></span> <span data-ttu-id="3f504-993">**[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-993">Select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="3f504-994">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-994">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-995">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-995">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-996">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-996">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-997">**LogFiles** フォルダーを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-997">Select the **LogFiles** folder.</span></span>
1. <span data-ttu-id="3f504-998">**[Modified]\(変更日\)** 列を調べて、変更日が最新の stdout ログの鉛筆アイコンを選んで編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-998">Inspect the **Modified** column and select the pencil icon to edit the stdout log with the latest modification date.</span></span>
1. <span data-ttu-id="3f504-999">ログ ファイルが開くと、エラーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-999">When the log file opens, the error is displayed.</span></span>

<span data-ttu-id="3f504-1000">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1000">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="3f504-1001">Kudu の**診断コンソール** で、パス **site** > **wwwroot** に戻り、*web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1001">In the Kudu **Diagnostic Console**, return to the path **site** > **wwwroot** to reveal the *web.config* file.</span></span> <span data-ttu-id="3f504-1002">鉛筆アイコンを選んで **web.config** ファイルを再び開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1002">Open the **web.config** file again by selecting the pencil icon.</span></span>
1. <span data-ttu-id="3f504-1003">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1003">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="3f504-1004">**[保存]** を選んでファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1004">Select **Save** to save the file.</span></span>

<span data-ttu-id="3f504-1005">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1005">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-1006">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1006">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-1007">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-1007">There's no limit on log file size or the number of log files created.</span></span> <span data-ttu-id="3f504-1008">stdout ログは、アプリ起動時の問題のトラブルシューティングにのみ使ってください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1008">Only use stdout logging to troubleshoot app startup problems.</span></span>
>
> <span data-ttu-id="3f504-1009">起動後の ASP.NET Core アプリでの一般的なログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-1009">For general logging in an ASP.NET Core app after startup, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-1010">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1010">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="slow-or-hanging-app-azure-app-service"></a><span data-ttu-id="3f504-1011">遅いアプリまたはハングしているアプリ (Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="3f504-1011">Slow or hanging app (Azure App Service)</span></span>

<span data-ttu-id="3f504-1012">要求に対してアプリの反応が遅い場合、またはハングする場合は、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1012">When an app responds slowly or hangs on a request, see the following articles:</span></span>

* [<span data-ttu-id="3f504-1013">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-1013">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="3f504-1014">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App (クラッシュ診断サイト拡張機能を使用して Azure Web App での断続的な例外の問題またはパフォーマンスの問題用のダンプをキャプチャする)</span><span class="sxs-lookup"><span data-stu-id="3f504-1014">Use Crash Diagnoser Site Extension to Capture Dump for Intermittent Exception issues or performance issues on Azure Web App</span></span>](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a><span data-ttu-id="3f504-1015">監視ブレード</span><span class="sxs-lookup"><span data-stu-id="3f504-1015">Monitoring blades</span></span>

<span data-ttu-id="3f504-1016">監視ブレードでは、このトピックでこれまでに説明した方法に代わるトラブルシューティング エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1016">Monitoring blades provide an alternative troubleshooting experience to the methods described earlier in the topic.</span></span> <span data-ttu-id="3f504-1017">これらのブレードは、500 番台のエラーの診断に使用できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1017">These blades can be used to diagnose 500-series errors.</span></span>

<span data-ttu-id="3f504-1018">ASP.NET Core 拡張機能がインストールされていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1018">Confirm that the ASP.NET Core Extensions are installed.</span></span> <span data-ttu-id="3f504-1019">拡張機能がインストールされていない場合は、手動でインストールします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1019">If the extensions aren't installed, install them manually:</span></span>

1. <span data-ttu-id="3f504-1020">**[開発ツール]** ブレード セクションで、 **[拡張機能]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1020">In the **DEVELOPMENT TOOLS** blade section, select the **Extensions** blade.</span></span>
1. <span data-ttu-id="3f504-1021">**[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** が一覧に表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1021">The **ASP.NET Core Extensions** should appear in the list.</span></span>
1. <span data-ttu-id="3f504-1022">拡張機能がインストールされていない場合は、 **[追加]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1022">If the extensions aren't installed, select the **Add** button.</span></span>
1. <span data-ttu-id="3f504-1023">一覧から **[ASP.NET Core Extensions]\(ASP.NET Core 拡張機能\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1023">Choose the **ASP.NET Core Extensions** from the list.</span></span>
1. <span data-ttu-id="3f504-1024">**[OK]** を選んで法的条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1024">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="3f504-1025">**[拡張機能の追加]** ブレードで **[OK]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1025">Select **OK** on the **Add extension** blade.</span></span>
1. <span data-ttu-id="3f504-1026">拡張機能が正常にインストールされると、情報ポップアップ メッセージで通知されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1026">An informational pop-up message indicates when the extensions are successfully installed.</span></span>

<span data-ttu-id="3f504-1027">stdout ログが有効になっていない場合は、次の手順のようにします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1027">If stdout logging isn't enabled, follow these steps:</span></span>

1. <span data-ttu-id="3f504-1028">Azure portal の **[開発ツール]** 領域で **[高度なツール]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1028">In the Azure portal, select the **Advanced Tools** blade in the **DEVELOPMENT TOOLS** area.</span></span> <span data-ttu-id="3f504-1029">**[Go&rarr;]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1029">Select the **Go&rarr;** button.</span></span> <span data-ttu-id="3f504-1030">新しいブラウザー タブまたはウィンドウで Kudu コンソールが開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1030">The Kudu console opens in a new browser tab or window.</span></span>
1. <span data-ttu-id="3f504-1031">ページの上部にあるナビゲーション バーを使って **[デバッグ コンソール]** を開き、 **[CMD]** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1031">Using the navigation bar at the top of the page, open **Debug console** and select **CMD**.</span></span>
1. <span data-ttu-id="3f504-1032">パス **site** > **wwwroot** へのフォルダーを開き、下にスクロールして、一覧の最後にある *web.config* ファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1032">Open the folders to the path **site** > **wwwroot** and scroll down to reveal the *web.config* file at the bottom of the list.</span></span>
1. <span data-ttu-id="3f504-1033">*web.config* ファイルの隣の鉛筆アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1033">Click the pencil icon next to the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-1034">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** パスを `\\?\%home%\LogFiles\stdout` に変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1034">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to: `\\?\%home%\LogFiles\stdout`.</span></span>
1. <span data-ttu-id="3f504-1035">**[保存]** を選んで、更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1035">Select **Save** to save the updated *web.config* file.</span></span>

<span data-ttu-id="3f504-1036">続けて診断ログをアクティブにします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1036">Proceed to activate diagnostic logging:</span></span>

1. <span data-ttu-id="3f504-1037">Azure portal で **[診断ログ]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1037">In the Azure portal, select the **Diagnostics logs** blade.</span></span>
1. <span data-ttu-id="3f504-1038">**[アプリケーション ログ (ファイル システム)]** および **[詳細なエラー メッセージ]** の **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1038">Select the **On** switch for **Application Logging (Filesystem)** and **Detailed error messages**.</span></span> <span data-ttu-id="3f504-1039">ブレードの上部にある **[保存]** ボタンを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1039">Select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="3f504-1040">失敗した要求イベントのバッファ処理 (FREB) とも呼ばれる失敗した要求のトレースを含めるには、 **[失敗した要求のトレース]** で **[オン]** スイッチを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1040">To include failed request tracing, also known as Failed Request Event Buffering (FREB) logging, select the **On** switch for **Failed request tracing**.</span></span>
1. <span data-ttu-id="3f504-1041">ポータルで **[診断ログ]** ブレードのすぐ下にある **[ログ ストリーム]** ブレードを選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1041">Select the **Log stream** blade, which is listed immediately under the **Diagnostics logs** blade in the portal.</span></span>
1. <span data-ttu-id="3f504-1042">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1042">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-1043">ログ ストリーム データ内で、エラーの原因が示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1043">Within the log stream data, the cause of the error is indicated.</span></span>

<span data-ttu-id="3f504-1044">トラブルシューティングが完了したら、stdout ログを無効にしてください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1044">Be sure to disable stdout logging when troubleshooting is complete.</span></span>

<span data-ttu-id="3f504-1045">失敗した要求のトレース ログ (FREB ログ) を見るには:</span><span class="sxs-lookup"><span data-stu-id="3f504-1045">To view the failed request tracing logs (FREB logs):</span></span>

1. <span data-ttu-id="3f504-1046">Azure portal で **[問題の診断と解決]** ブレードに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1046">Navigate to the **Diagnose and solve problems** blade in the Azure portal.</span></span>
1. <span data-ttu-id="3f504-1047">サイド バーの **[SUPPORT TOOLS]\(サポート ツール\)** 領域で、 **[Failed Request Tracing Logs]\(失敗した要求のトレース ログ\)** を選びます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1047">Select **Failed Request Tracing Logs** from the **SUPPORT TOOLS** area of the sidebar.</span></span>

<span data-ttu-id="3f504-1048">詳しくは、[「Azure App Service の Web アプリの診断ログの有効化」トピックの「失敗した要求トレース」セクション](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)および[「Azure での Web アプリのアプリケーション パフォーマンスに関するよくあるご質問」の「失敗した要求トレースをオンにするにはどうすればよいですか?」](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1048">See [Failed request traces section of the Enable diagnostics logging for web apps in Azure App Service topic](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) and the [Application performance FAQs for Web Apps in Azure: How do I turn on failed request tracing?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) for more information.</span></span>

<span data-ttu-id="3f504-1049">詳しくは、「[Azure App Service の Web アプリの診断ログの有効化](/azure/app-service/web-sites-enable-diagnostic-log)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1049">For more information, see [Enable diagnostics logging for web apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-1050">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1050">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-1051">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-1051">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="3f504-1052">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-1052">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-1053">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1053">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="troubleshoot-on-iis"></a><span data-ttu-id="3f504-1054">IIS でのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-1054">Troubleshoot on IIS</span></span>

### <a name="application-event-log-iis"></a><span data-ttu-id="3f504-1055">アプリケーション イベント ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-1055">Application Event Log (IIS)</span></span>

<span data-ttu-id="3f504-1056">アプリケーション イベント ログにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1056">Access the Application Event Log:</span></span>

1. <span data-ttu-id="3f504-1057">[スタート] メニューを開き、*イベント ビューアー*を検索し、**イベント ビューアー** アプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1057">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="3f504-1058">**イベント ビューアー**で **[Windows ログ]** ノードを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1058">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="3f504-1059">**[Application]** を選択して、アプリケーション イベント ログを開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1059">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="3f504-1060">失敗したアプリに関連するエラーを検索します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1060">Search for errors associated with the failing app.</span></span> <span data-ttu-id="3f504-1061">エラーの *[ソース]* 列には *IIS AspNetCore Module* または *IIS Express AspNetCore Module* の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1061">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="3f504-1062">コマンド プロンプトでアプリを実行する</span><span class="sxs-lookup"><span data-stu-id="3f504-1062">Run the app at a command prompt</span></span>

<span data-ttu-id="3f504-1063">多くの起動時エラーでは、アプリケーション イベント ログに役に立つ情報が生成されません。</span><span class="sxs-lookup"><span data-stu-id="3f504-1063">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="3f504-1064">ホスティング システムのコマンド プロンプトでアプリを実行すると、エラーの原因がわかることがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1064">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="3f504-1065">フレームワークに依存する展開</span><span class="sxs-lookup"><span data-stu-id="3f504-1065">Framework-dependent deployment</span></span>

<span data-ttu-id="3f504-1066">アプリが[フレームワークに依存する展開](/dotnet/core/deploying/#framework-dependent-deployments-fdd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-1066">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="3f504-1067">コマンド プロンプトで展開フォルダーに移動し、*dotnet.exe* 使用してアプリのアセンブリを実行して、アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1067">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="3f504-1068">コマンド `dotnet .\<assembly_name>.dll` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1068">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="3f504-1069">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1069">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="3f504-1070">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1070">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="3f504-1071">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="3f504-1071">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="3f504-1072">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1072">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="3f504-1073">自己完結型の展開</span><span class="sxs-lookup"><span data-stu-id="3f504-1073">Self-contained deployment</span></span>

<span data-ttu-id="3f504-1074">アプリが[自己完結型の展開](/dotnet/core/deploying/#self-contained-deployments-scd)の場合:</span><span class="sxs-lookup"><span data-stu-id="3f504-1074">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="3f504-1075">コマンド プロンプトで、展開フォルダーに移動し、アプリの実行可能ファイルを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1075">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="3f504-1076">コマンド `<assembly_name>.exe` の \<assembly_name> にアプリのアセンブリの名前を指定して実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1076">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="3f504-1077">エラーを示すアプリからのコンソール出力は、すべてコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1077">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="3f504-1078">アプリへの要求時にエラーが発生した場合は、Kestrel がリッスンしているホストとポートに要求が送信されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1078">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="3f504-1079">既定のホストと post を使用して `http://localhost:5000/` に要求を行います。</span><span class="sxs-lookup"><span data-stu-id="3f504-1079">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="3f504-1080">アプリが Kestrel のエンドポイント アドレスで正常に応答する場合、問題はホスティングの構成に関連している可能性が高く、アプリ内が原因の可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1080">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log-iis"></a><span data-ttu-id="3f504-1081">ASP.NET Core モジュールの stdout ログ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-1081">ASP.NET Core Module stdout log (IIS)</span></span>

<span data-ttu-id="3f504-1082">stdout ログを有効にして表示するには:</span><span class="sxs-lookup"><span data-stu-id="3f504-1082">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="3f504-1083">ホスティング システム上のサイトの展開フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1083">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="3f504-1084">*logs* フォルダーが存在しない場合は、フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1084">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="3f504-1085">MSBuild で展開フォルダーに *logs* フォルダーを自動的に作成されるようにする手順については、「[ディレクトリ構造](xref:host-and-deploy/directory-structure)」のトピックを参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1085">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="3f504-1086">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1086">Edit the *web.config* file.</span></span> <span data-ttu-id="3f504-1087">**stdoutLogEnabled** を `true` に設定し、**stdoutLogFile** のパスを *logs* フォルダー (たとえば `.\logs\stdout`) を指すように変更します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1087">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="3f504-1088">パスの `stdout` はログ ファイル名のプレフィックスです。</span><span class="sxs-lookup"><span data-stu-id="3f504-1088">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="3f504-1089">ログ ファイルの作成時には、タイムスタンプ、プロセス ID、およびファイルの拡張子が自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1089">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="3f504-1090">ファイル名のプレフィックスとして `stdout` を使用すると、一般的なログ ファイルの名前は *stdout_20180205184032_5412.log* になります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1090">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="3f504-1091">アプリケーション プールの ID に *logs* フォルダーへの書き込みアクセス許可があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1091">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="3f504-1092">更新した *web.config* ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1092">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="3f504-1093">アプリに対して要求します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1093">Make a request to the app.</span></span>
1. <span data-ttu-id="3f504-1094">*logs* フォルダーに移動します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1094">Navigate to the *logs* folder.</span></span> <span data-ttu-id="3f504-1095">最新の stdout ログを探して開きます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1095">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="3f504-1096">ログのエラーを調べます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1096">Study the log for errors.</span></span>

<span data-ttu-id="3f504-1097">トラブルシューティングが完了したら、stdout ログを無効にします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1097">Disable stdout logging when troubleshooting is complete:</span></span>

1. <span data-ttu-id="3f504-1098">*web.config* ファイルを編集します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1098">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="3f504-1099">**stdoutLogEnabled** を `false` に設定します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1099">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="3f504-1100">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1100">Save the file.</span></span>

<span data-ttu-id="3f504-1101">詳細については、「<xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1101">For more information, see <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-1102">stdout ログを無効にしないと、アプリまたはサーバーで障害が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1102">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="3f504-1103">ログ ファイルのサイズまたは作成されるログ ファイルの数に制限はありません。</span><span class="sxs-lookup"><span data-stu-id="3f504-1103">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="3f504-1104">ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。</span><span class="sxs-lookup"><span data-stu-id="3f504-1104">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="3f504-1105">詳細については、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1105">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

### <a name="enable-the-developer-exception-page"></a><span data-ttu-id="3f504-1106">開発者例外ページを有効にする</span><span class="sxs-lookup"><span data-stu-id="3f504-1106">Enable the Developer Exception Page</span></span>

<span data-ttu-id="3f504-1107">開発環境でアプリを実行するには、`ASPNETCORE_ENVIRONMENT` [環境変数を web.config に追加することができます](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。</span><span class="sxs-lookup"><span data-stu-id="3f504-1107">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="3f504-1108">アプリの起動時にホスト ビルダー上で `UseEnvironment` によって環境がオーバーライドされない限り、環境変数を設定すると、アプリの実行時に[開発者例外ページ](xref:fundamentals/error-handling)が表示されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1108">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="3f504-1109">`ASPNETCORE_ENVIRONMENT` の環境変数を設定する方法は、インターネットに公開されないステージング サーバーやテスト用サーバーの場合にのみお勧めされます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1109">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="3f504-1110">トラブルシューティング後は、必ず *web.config* ファイルからこの環境変数を削除してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1110">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="3f504-1111">*web.config* で環境変数を設定する方法の詳細については、[aspNetCore の environmentVariables 子要素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1111">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

### <a name="obtain-data-from-an-app"></a><span data-ttu-id="3f504-1112">アプリからデータを取得する</span><span class="sxs-lookup"><span data-stu-id="3f504-1112">Obtain data from an app</span></span>

<span data-ttu-id="3f504-1113">アプリが要求に応答できる場合は、ターミナル インライン ミドルウェアを使用して、要求、接続、その他のデータをアプリから取得します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1113">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="3f504-1114">詳細およびサンプル コードについては、「<xref:test/troubleshoot#obtain-data-from-an-app>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1114">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

### <a name="slow-or-hanging-app-iis"></a><span data-ttu-id="3f504-1115">遅いアプリまたはハングしているアプリ (IIS)</span><span class="sxs-lookup"><span data-stu-id="3f504-1115">Slow or hanging app (IIS)</span></span>

<span data-ttu-id="3f504-1116">"*クラッシュ ダンプ*" とはシステムのメモリのスナップショットであり、アプリのクラッシュ、起動の失敗、遅いアプリの原因を突き止めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1116">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="3f504-1117">アプリのクラッシュまたは例外の発生</span><span class="sxs-lookup"><span data-stu-id="3f504-1117">App crashes or encounters an exception</span></span>

<span data-ttu-id="3f504-1118">[Windows エラー報告 (WER)](/windows/desktop/wer/windows-error-reporting) からダンプを取得して分析します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1118">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="3f504-1119">`c:\dumps` にクラッシュ ダンプ ファイルを保持するフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1119">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="3f504-1120">アプリ プールには、そのフォルダーへの書き込みアクセス権が必要です。</span><span class="sxs-lookup"><span data-stu-id="3f504-1120">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="3f504-1121">[EnableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) を実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1121">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="3f504-1122">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1122">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="3f504-1123">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1123">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="3f504-1124">クラッシュが発生する条件の下でアプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1124">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="3f504-1125">クラッシュが発生した後、[DisableDumps PowerShell スクリプト](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)を実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1125">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="3f504-1126">アプリで[インプロセス ホスティング モデル](xref:host-and-deploy/iis/index#in-process-hosting-model)が使われている場合は、*w3wp.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1126">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="3f504-1127">アプリで[アウト プロセス ホスティング モデル](xref:host-and-deploy/iis/index#out-of-process-hosting-model)が使われている場合は、*dotnet.exe* に対するスクリプトを実行します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1127">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="3f504-1128">アプリがクラッシュし、ダンプの収集が完了したら、アプリを普通に終了してかまいません。</span><span class="sxs-lookup"><span data-stu-id="3f504-1128">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="3f504-1129">PowerShell スクリプトにより、アプリごとに最大 5 つのダンプを収集する WER が構成されます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1129">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="3f504-1130">クラッシュ ダンプでは、大量のディスク領域 (1 つにつき最大、数ギガバイト) が使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1130">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="3f504-1131">アプリが起動時または正常な実行中にハングまたは失敗する</span><span class="sxs-lookup"><span data-stu-id="3f504-1131">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="3f504-1132">アプリが起動時または正常な実行中に "*ハング*" (応答を停止するがクラッシュしない) または失敗するときは、「[User-Mode Dump Files:Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)」(ユーザー モード ダンプ ファイル: 最適なツールの選択) を参照し、適切なツールを選択してダンプを生成します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1132">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="3f504-1133">ダンプを分析する</span><span class="sxs-lookup"><span data-stu-id="3f504-1133">Analyze the dump</span></span>

<span data-ttu-id="3f504-1134">ダンプはいくつかの方法で分析できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1134">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="3f504-1135">詳細については、「[Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)」(ユーザー モード ダンプ ファイルの分析) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3f504-1135">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="clear-package-caches"></a><span data-ttu-id="3f504-1136">パッケージ キャッシュをクリアする</span><span class="sxs-lookup"><span data-stu-id="3f504-1136">Clear package caches</span></span>

<span data-ttu-id="3f504-1137">開発マシンで .NET Core SDK をアップグレードしたり、アプリ内のパッケージ バージョンを変更したりした直後に、機能しているアプリが失敗することがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1137">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="3f504-1138">場合によっては、パッケージに統一性がないと、メジャー アップグレード実行時にアプリが破壊されることがあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1138">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="3f504-1139">これらの問題のほとんどは、次の手順で解決できます。</span><span class="sxs-lookup"><span data-stu-id="3f504-1139">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="3f504-1140">*bin* フォルダーと *obj* フォルダーを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1140">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="3f504-1141">コマンド シェルから [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) を実行して、パッケージ キャッシュをクリアします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1141">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="3f504-1142">パッケージ キャッシュをクリアするには、[nuget.exe](https://www.nuget.org/downloads) ツールを使用して `nuget locals all -clear` コマンドを実行する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1142">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="3f504-1143">*nuget.exe* は、Windows デスクトップ オペレーティング システムにバンドルされているインストールではなく、[NuGet Web サイト](https://www.nuget.org/downloads)から別に入手する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3f504-1143">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="3f504-1144">プロジェクトを復元してリビルドします。</span><span class="sxs-lookup"><span data-stu-id="3f504-1144">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="3f504-1145">アプリを再展開する前に、サーバー上の展開フォルダー内のすべてのファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="3f504-1145">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3f504-1146">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="3f504-1146">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a><span data-ttu-id="3f504-1147">Azure のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-1147">Azure documentation</span></span>

* [<span data-ttu-id="3f504-1148">ASP.NET Core 用 Application Insights</span><span class="sxs-lookup"><span data-stu-id="3f504-1148">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* [<span data-ttu-id="3f504-1149">Visual Studio を使用した Azure App Service の Web アプリのトラブルシューティングの Web アプリのリモート デバッグ セクション</span><span class="sxs-lookup"><span data-stu-id="3f504-1149">Remote debugging web apps section of Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [<span data-ttu-id="3f504-1150">Azure App Service の診断の概要</span><span class="sxs-lookup"><span data-stu-id="3f504-1150">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* [<span data-ttu-id="3f504-1151">方法: Azure App Service でアプリを監視する</span><span class="sxs-lookup"><span data-stu-id="3f504-1151">How to: Monitor Apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)
* [<span data-ttu-id="3f504-1152">Visual Studio を使用した Azure App Service での Web アプリのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-1152">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="3f504-1153">Azure Web Apps での "502 bad gateway" および "503 service unavailable" の HTTP エラーのトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-1153">Troubleshoot HTTP errors of "502 bad gateway" and "503 service unavailable" in your Azure web apps</span></span>](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [<span data-ttu-id="3f504-1154">Azure App Service での Web アプリのパフォーマンス低下に関する問題のトラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="3f504-1154">Troubleshoot slow web app performance issues in Azure App Service</span></span>](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [<span data-ttu-id="3f504-1155">Azure での Web アプリのアプリケーションパフォーマンスに関するよくあるご質問</span><span class="sxs-lookup"><span data-stu-id="3f504-1155">Application performance FAQs for Web Apps in Azure</span></span>](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [<span data-ttu-id="3f504-1156">Azure Web アプリのサンドボックス (App Service ランタイムの実行の制限)</span><span class="sxs-lookup"><span data-stu-id="3f504-1156">Azure Web App sandbox (App Service runtime execution limitations)</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [<span data-ttu-id="3f504-1157">Azure Friday: Azure App Service の診断とトラブルシューティング (12 分間のビデオ)</span><span class="sxs-lookup"><span data-stu-id="3f504-1157">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a><span data-ttu-id="3f504-1158">Visual Studio ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-1158">Visual Studio documentation</span></span>

* [<span data-ttu-id="3f504-1159">Visual Studio 2017 における Azure の IIS での ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-1159">Remote Debug ASP.NET Core on IIS in Azure in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-azure)
* [<span data-ttu-id="3f504-1160">Visual Studio 2017 におけるリモートの IIS コンピューターでの ASP.NET Core のリモート デバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-1160">Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017</span></span>](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [<span data-ttu-id="3f504-1161">Visual Studio を使用したデバッグについて理解する</span><span class="sxs-lookup"><span data-stu-id="3f504-1161">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a><span data-ttu-id="3f504-1162">Visual Studio Code ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3f504-1162">Visual Studio Code documentation</span></span>

* [<span data-ttu-id="3f504-1163">Visual Studio Code でのデバッグ</span><span class="sxs-lookup"><span data-stu-id="3f504-1163">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
