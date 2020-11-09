---
title: アプリのオフライン ファイル (app_offline.htm)
author: rick-anderson
description: アプリのオフライン ファイル (`app_offline.htm`) が ASP.NET Core モジュールでどのように機能するかについて説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 4d71b95680a9b160ebb25116e35096495a2eaf93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058650"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="1e6fb-103">アプリのオフライン ファイル (`app_offline.htm`)</span><span class="sxs-lookup"><span data-stu-id="1e6fb-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="1e6fb-104">アプリのオフライン ファイル (`app_offline.htm`) は、アプリをシャットダウンするために ASP.NET Core モジュールによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="1e6fb-105">`app_offline.htm` という名前のファイルがアプリのルート ディレクトリで検出された場合、アプリを正常にシャットダウンし、受信要求の処理を停止することが、ASP.NET Core モジュールによって試みられます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="1e6fb-106">`shutdownTimeLimit` 内で定義されている秒数が経過してもまだアプリが実行されている場合、ASP.NET Core モジュールによって実行中のプロセスが停止されます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="1e6fb-107">`app_offline.htm` ファイルが存在している間、ASP.NET Core モジュールは、`app_offline.htm` ファイルの内容を返送することで、要求に応答します。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="1e6fb-108">`app_offline.htm` ファイルが削除されると、次の要求によってアプリが起動されます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="1e6fb-109">アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="1e6fb-110">たとえば、WebSocket 接続によってアプリのシャットダウンが遅れる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="1e6fb-111">ロックされた展開ファイル</span><span class="sxs-lookup"><span data-stu-id="1e6fb-111">Locked deployment files</span></span>

<span data-ttu-id="1e6fb-112">アプリが実行中は、展開フォルダー内のファイルがロックされます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="1e6fb-113">展開中にロックされたファイルを上書きすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="1e6fb-114">`app_offline.htm` は、ロックされたファイルを解放するための主要なメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="1e6fb-115">`app_offline.htm` は、アプリを適切に停止して起動するために Web 配置によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="1e6fb-116">`app_offline.htm` を手動で使用して、アプリを開始および停止することができます (PowerShell 5 以降が必要)。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

<span data-ttu-id="1e6fb-117">上記の PowerShell スクリプトでは、次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="1e6fb-118">プレースホルダー `{PATH TO APP}` は、アプリへのパスです。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="1e6fb-119">`New-Item` コマンドにより、アプリ プールが停止されます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="1e6fb-120">`Remove-Item` コマンドにより、アプリ プールが開始されます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="1e6fb-121">`New-Item` コマンドと `Remove-Item` コマンドの間にあるコマンドは、アプリを配置するために開発者が提供したものです。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="1e6fb-122">サーバー上の IIS マネージャーでアプリ プールを手動で停止することで、ファイルのロックを解除することもできます。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="1e6fb-123">IIS マネージャーを使用してアプリ プールを停止したり、再起動したりするときは、`app_offine.htm` ファイルを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1e6fb-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
