---
title: ASP.NET Core モジュールを使用したログの作成とリダイレクト
author: rick-anderson
description: ログと診断情報をキャプチャするように IIS と ASP.NET Core モジュールを構成します。
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: 523eec53d7d21723dcf136c4e5ce299533a78cc6
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91901026"
---
# <a name="log-creation-and-redirection"></a>ログの作成とリダイレクト

`aspNetCore` 要素の `stdoutLogEnabled` 属性および `stdoutLogFile` 属性が設定されている場合は、stdout および stderr コンソール出力が ASP.NET Core モジュールによってディスクにリダイレクトされます。 `stdoutLogFile` パスのフォルダーは、ログ ファイルの作成時、モジュールによって作成されます。 アプリ プールには、ログが書き込まれる場所への書き込みアクセス権が付与されている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\{APP POOL NAME}` を使用します。ここで、プレースホルダー `{APP POOL NAME}` はアプリ プール名です)。

プロセスのリサイクル/再起動が発生しない場合、ログは循環されません。 ログが使用するディスク領域を制限するのは、ホストの役割です。

stdout ログの使用は、IIS でホストするときか、[Visual Studio の開発時 IIS サポート](xref:host-and-deploy/iis/development-time-iis-support)を使用するとき、アプリの起動問題を解決する場合にのみ推奨されます。ローカル デバッグ時、IIS Express でアプリを実行している場合は推奨されません。

一般的なアプリ ログの目的には、stdout ログを使わないでください。 ASP.NET Core アプリでのルーチン ログの場合は、ログ ファイルのサイズを制限し、ログをローテーションするログ ライブラリを使います。 詳しくは、「[サードパーティ製のログ プロバイダー](xref:fundamentals/logging/index#third-party-logging-providers)」をご覧ください。

ログ ファイルの作成時には、タイムスタンプとファイルの拡張子が自動的に追加されます。 ログ ファイル名は、タイムスタンプ、プロセス ID、およびファイル拡張子 ( `.log`) を `stdoutLogFile` パスの最後のセグメント (通常は `stdout`) にアンダースコアで区切って追加することで構成されます。 `stdoutLogFile` パスが `stdout` で終わっている場合、PID が 1934 で 2018 年 2 月 5 日の 19:42:32 に作成されたアプリのログのファイル名は、`stdout_20180205194132_1934.log` になります。

`stdoutLogEnabled` が false の場合は、アプリの起動時に発生するエラーがキャプチャされ、30 KB までイベント ログに出力されます。 起動後、すべての追加のログが破棄されます。

次のサンプルでは、`aspNetCore` 要素により相対パス `.\log\` で stdout ログ記録が構成されます。 AppPool のユーザー ID に、指定されたパスへの書き込みアクセス許可があることを確認してください。

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure App Service デプロイのためにアプリを公開するとき、Web SDK により `stdoutLogFile` 値が `\\?\%home%\LogFiles\stdout` に設定されます。 Azure App Service でホストされるアプリの場合、`%home` 環境変数が事前定義されます。

ログ記録のフィルター ルールを作成する方法については、ASP.NET Core ログ記録文書の「[構成](xref:fundamentals/logging/index#log-filtering)」セクションと「[フィルタリング](xref:fundamentals/logging/index#log-filtering)」セクションを参照してください。

パス形式の詳細については、「[Windows システムのファイル パス形式](/dotnet/standard/io/file-path-formats)」を参照してください。

## <a name="enhanced-diagnostic-logs"></a>強化された診断ログ

ASP.NET Core モジュールは、強化された診断ログを提供するよう構成できます。 `<handlerSettings>` 要素を、`web.config` 内の `<aspNetCore>` 要素に追加します。 `debugLevel` を `TRACE` に設定すると、診断情報が再現性の高いものになります。

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

パスに含まれるフォルダー (前の例では `logs`) は、ログ ファイルの作成時に、モジュールによって作成されます。 アプリ プールには、ログが書き込まれる場所への書き込みアクセス権が付与されている必要があります (書き込みアクセス許可を提供するには、`IIS AppPool\{APP POOL NAME}` を使用します。ここで、プレースホルダー `{APP POOL NAME}` はアプリ プール名です)。

デバッグ レベル (`debugLevel`) 値には、レベルと場所の両方を含めることができます。

レベルは次のとおりです (情報量が少ないものから多いものへの順)。

* ERROR
* WARNING
* INFO
* TRACE

場所は次のとおりです (複数の場所を指定できます)。

* CONSOLE
* EVENTLOG
* ファイル

ハンドラー設定は、次の環境変数を使用して指定することもできます。

* `ASPNETCORE_MODULE_DEBUG_FILE`:デバッグ ログ ファイルのパス。 (既定値: `aspnetcore-debug.log`)
* `ASPNETCORE_MODULE_DEBUG`:デバッグ レベルの設定。

> [!WARNING]
> 配置内でデバッグ ログを、問題のトラブルシューティングに必要な時間よりも長く有効のままに**しないでください**。 ログのサイズは制限されていません。 デバッグ ログを有効のままにすると、使用可能なディスク領域が使い果たされ、サーバーまたはアプリ サービスがクラッシュする可能性があります。

`web.config` ファイル内の `aspNetCore` 要素の例については、「[`web.config` を使用した ASP.NET Core モジュールの構成](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig)」を参照してください。
