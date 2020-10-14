---
title: ホスティング バンドル
author: rick-anderson
description: .NET Core ホスティング バンドルを構成する方法について説明します。
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755177"
---
# <a name="the-net-core-hosting-bundle"></a>.NET Core ホスティング バンドル

.NET Core ホスティング バンドルは、.NET Core ランタイムおよび [ASP.NET Core モジュール](xref:host-and-deploy/aspnet-core-module)用のインストーラーです。 バンドルを使用すると、ASP.NET Core アプリを IIS で実行できます。

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core ホスティング バンドルのインストール

> [!IMPORTANT]
> ホスティング バンドルが IIS の前にインストールされている場合、バンドルのインストールを修復する必要があります。 IIS をインストールした後に、ホスティング バンドル インストーラーをもう一度実行します。
>
> .NET Core の 64 ビット (x64) バージョンをインストールした後、ホスティング バンドルをインストールした場合は、SDK が表示されない可能性があります ([.NET Core SDK が検出されない](xref:test/troubleshoot#no-net-core-sdks-were-detected))。 この問題を解決するには、<xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> を参照してください。

### <a name="direct-download-current-version"></a>直接ダウンロード (現在のバージョン)

次のリンクを使用してインストーラーをダウンロードします。

[現在の .NET Core ホスティング バンドルのインストーラー (直接ダウンロード)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>以前のバージョンのインストーラー

以前のバージョンのインストーラーを入手するには:

1. 「[.NET Core のダウンロード](https://dotnet.microsoft.com/download/dotnet-core)」ページに移動します。
1. 目的の .NET Core のバージョンを選択します。
1. **[Run apps - Runtime]\(アプリの実行 - ランタイム\)** 列から、目的の .NET Core ランタイム バージョンの行を探します。
1. **[Hosting Bundle]\(ホスティング バンドル\)** のリンクを使用してインストーラーをダウンロードします。

> [!WARNING]
> 一部のインストーラーには、有効期限 (EOL) に達して Microsoft によるサポートが終了した、リリース バージョンが含まれています。 詳細については、[サポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)をご覧ください。

### <a name="install-the-hosting-bundle"></a>ホスティング バンドルをインストールする

1. サーバーでインストーラーを実行します。 管理者のコマンド シェルからインストーラーを実行する場合、次のパラメーターを使用できます。

   * `OPT_NO_ANCM=1`:ASP.NET Core モジュールのインストールをスキップします。
   * `OPT_NO_RUNTIME=1`:.NET Core ランタイムのインストールをスキップします。 サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。
   * `OPT_NO_SHAREDFX=1`:ASP.NET Shared Framework (ASP.NET ランタイム) のインストールをスキップします。 サーバーが[自己完結型の展開 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) のみをホストする場合に使用します。
   * `OPT_NO_X86=1`:x86 ランタイムのインストールをスキップします。 32 ビット アプリをホストしない場合は、このパラメーターを使用します。 今後、32 ビットと 64 ビットのアプリ両方をホストする可能性がある場合は、このパラメーターを使用せずに、両方のランタイムをインストールします。
   * `OPT_NO_SHARED_CONFIG_CHECK=1`:共有構成 (`applicationHost.config`) が IIS のインストールと同じマシン上にある場合、IIS 共有構成を使用するためのチェックを無効にします。 "*ASP.NET Core 2.2 以降の Hosting Bundler インストーラーに対してのみ使用できます。* " 詳細については、「<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>」を参照してください。
1. システムを再起動するか、コマンドシェルで次のコマンドを実行します。

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS を再起動すると、インストーラーによって行われたシステム パスへの変更 (環境変数) が取得されます。

ASP.NET Core の共有フレームワーク パッケージの修正プログラムのリリースでは、ロールフォワード動作は採用されていません。 新しいホスティング バンドルをインストールして共有フレームワークをアップグレードした後、システムを再起動するか、コマンドシェルで次のコマンドを実行します。

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> IIS 共有構成の詳細については、「[ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)」 (IIS 共有構成の ASP.NET Core モジュール) を参照してください。

## <a name="module-version-and-hosting-bundle-installer-logs"></a>モジュールのバージョンとホスティング バンドル インストーラーのログ

インストールされている ASP.NET Core モジュールのバージョンを確認するには:

1. ホスティング システム上で、 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` に移動します。
1. `aspnetcorev2.dll` ファイルを見つけます。
1. ファイルを右クリックし、コンテキスト メニューの **[プロパティ]** を選びます。
1. **[詳細]** タブを選びます。 **[ファイル バージョン]** と **[製品バージョン]** が、インストールされているモジュールのバージョンを表します。

モジュールに関するホスティング バンドル インストーラーのログは、`C:\Users\%UserName%\AppData\Local\Temp` にあります。 このファイルには `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` という名前が付けられます。ここで、プレースホルダー `{TIMESTAMP}` はファイルのタイムスタンプです。
