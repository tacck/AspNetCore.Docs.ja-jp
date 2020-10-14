---
title: アプリのオフライン ファイル (app_offline.htm)
author: rick-anderson
description: アプリのオフライン ファイル (`app_offline.htm`) が ASP.NET Core モジュールでどのように機能するかについて説明します。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 95dfadd084af5909fee754308ad5d65f54d4875d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755189"
---
# <a name="app-offline-file-app_offlinehtm"></a>アプリのオフライン ファイル (`app_offline.htm`)

アプリのオフライン ファイル (`app_offline.htm`) は、アプリをシャットダウンするために ASP.NET Core モジュールによって使用されます。

`app_offline.htm` という名前のファイルがアプリのルート ディレクトリで検出された場合、アプリを正常にシャットダウンし、受信要求の処理を停止することが、ASP.NET Core モジュールによって試みられます。 `shutdownTimeLimit` 内で定義されている秒数が経過してもまだアプリが実行されている場合、ASP.NET Core モジュールによって実行中のプロセスが停止されます。

`app_offline.htm` ファイルが存在している間、ASP.NET Core モジュールは、`app_offline.htm` ファイルの内容を返送することで、要求に応答します。 `app_offline.htm` ファイルが削除されると、次の要求によってアプリが起動されます。

アウト プロセス ホスティング モデルを使用するときは、開いている接続があると、アプリがすぐにシャットダウンされない可能性があります。 たとえば、WebSocket 接続によってアプリのシャットダウンが遅れる可能性があります。

## <a name="locked-deployment-files"></a>ロックされた展開ファイル

アプリが実行中は、展開フォルダー内のファイルがロックされます。 展開中にロックされたファイルを上書きすることはできません。

`app_offline.htm` は、ロックされたファイルを解放するための主要なメカニズムです。 `app_offline.htm` は、アプリを適切に停止して起動するために Web 配置によって使用されます。

`app_offline.htm` を手動で使用して、アプリを開始および停止することができます (PowerShell 5 以降が必要)。

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

上記の PowerShell スクリプトでは、次のようになっています。

* プレースホルダー `{PATH TO APP}` は、アプリへのパスです。
* `New-Item` コマンドにより、アプリ プールが停止されます。
* `Remove-Item` コマンドにより、アプリ プールが開始されます。
* `New-Item` コマンドと `Remove-Item` コマンドの間にあるコマンドは、アプリを配置するために開発者が提供したものです。

サーバー上の IIS マネージャーでアプリ プールを手動で停止することで、ファイルのロックを解除することもできます。 IIS マネージャーを使用してアプリ プールを停止したり、再起動したりするときは、`app_offine.htm` ファイルを使用しないでください。
