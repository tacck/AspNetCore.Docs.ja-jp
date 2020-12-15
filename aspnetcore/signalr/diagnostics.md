---
title: ASP.NET Core でのログ記録と診断 SignalR
author: anurse
description: ASP.NET Core アプリから診断を収集する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr, devx-track-js
ms.date: 06/12/2020
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
uid: signalr/diagnostics
ms.openlocfilehash: 49029bbd53b98425a05bdb82517238e3aa2e3b1f
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506631"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a>ASP.NET Core でのログ記録と診断 SignalR

By [Andrew Stanton-看護師](https://twitter.com/anurse)

この記事では、ASP.NET Core アプリから診断情報を収集して問題のトラブルシューティングを行うためのガイダンスを提供 SignalR します。

## <a name="server-side-logging"></a>サーバー側のログ記録

> [!WARNING]
> サーバー側のログには、アプリからの機密情報が含まれる場合があります。 運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿 **しないでください**。

SignalRは ASP.NET Core の一部であるため、ASP.NET Core ログシステムを使用します。 既定の構成では、は SignalR ごくわずかな情報をログに記録しますが、これは構成できます。 ASP.NET Core ログの構成の詳細については、[ASP.NET Core ログ](xref:fundamentals/logging/index#configuration)に関するドキュメントを参照してください。

SignalR 2つの logger カテゴリを使用します。

* `Microsoft.AspNetCore.SignalR`: ハブプロトコルに関連するログ、ハブのアクティブ化、メソッドの呼び出し、およびその他のハブ関連アクティビティ。
* `Microsoft.AspNetCore.Http.Connections`: Websocket、長いポーリング、Server-Sent イベント、低レベルのインフラストラクチャなどのトランスポートに関連するログ。 SignalR

から詳細なログを有効にするには SignalR 、の `Debug` *appsettings.json* サブセクションに次の項目を追加して、前のプレフィックスをファイル内のレベルに構成し `LogLevel` `Logging` ます。

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

これは、メソッドのコードで構成することもでき `CreateWebHostBuilder` ます。

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

JSON ベースの構成を使用していない場合は、構成システムで次の構成値を設定します。

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

構成システムのドキュメントを調べて、入れ子になった構成値を指定する方法を確認してください。 たとえば、環境変数の使用時には、`:` の代わりに 2 つの `_` 文字を使用します (例: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`)。

アプリのより詳細な診断情報を収集する場合は、`Debug` レベルを使用することが推奨されます。 `Trace` レベルでは非常に低レベルの診断情報が生成されるため、アプリの問題を診断するために必要となることはまれです。

## <a name="access-server-side-logs"></a>サーバー側ログにアクセスする

サーバー側のログにアクセスする方法は、実行環境によって異なります。

### <a name="as-a-console-app-outside-iis"></a>IIS の外部のコンソールアプリとして

コンソール アプリで実行中の場合は、[コンソール ロガー](xref:fundamentals/logging/index#console)を既定で有効にする必要があります。 SignalR ログはコンソールに表示されます。

### <a name="within-iis-express-from-visual-studio"></a>Visual Studio から IIS Express 内

Visual Studio では、[ **出力** ] ウィンドウにログ出力が表示されます。 [ **ASP.NET Core Web サーバー** ] ドロップダウンオプションを選択します。

### <a name="azure-app-service"></a>Azure App Service

Azure App Service ポータルの [**診断ログ**] セクションで [**アプリケーションログ (ファイルシステム)** ] オプションを有効にし、**レベル** をに構成し `Verbose` ます。 ログは、 **ログストリーミング** サービスおよび App Service のファイルシステムのログで使用できます。 詳細については、「 [Azure ログストリーミング](xref:fundamentals/logging/index#azure-log-streaming)」を参照してください。

### <a name="other-environments"></a>その他の環境

アプリが別の環境 (Docker、Kubernetes、Windows サービスなど) にデプロイされる場合は、その環境に適したログ プロバイダーを構成する方法についての詳細を、「<xref:fundamentals/logging/index>」で確認してください。

## <a name="javascript-client-logging"></a>JavaScript クライアントのログ記録

> [!WARNING]
> クライアント側のログには、アプリからの機密情報が含まれる場合があります。 運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿 **しないでください**。

JavaScript クライアントを使用する場合は、でメソッドを使用してログオプションを構成でき `configureLogging` `HubConnectionBuilder` ます。

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。

次の表は、JavaScript クライアントで使用できるログレベルを示しています。 ログレベルをこれらの値のいずれかに設定すると、そのレベルおよびテーブル内のすべてのレベルでログ記録が有効になります。

| Level | 説明 |
| ----- | ----------- |
| `None` | メッセージはログに記録されません。 |
| `Critical` | アプリ全体でエラーが発生したことを示すメッセージ。 |
| `Error` | 現在の操作でエラーが発生したことを示すメッセージ。 |
| `Warning` | 致命的ではない問題を示すメッセージ。 |
| `Information` | 情報メッセージ。 |
| `Debug` | デバッグに役立つ診断メッセージ。 |
| `Trace` | 特定の問題を診断するために設計された、非常に詳細な診断メッセージ。 |

詳細設定を構成すると、ログはブラウザーコンソール (または NodeJS アプリの標準出力) に書き込まれます。

カスタムログシステムにログを送信する場合は、インターフェイスを実装する JavaScript オブジェクトを指定でき `ILogger` ます。 実装する必要のあるメソッドは `log` 、イベントのレベルとイベントに関連付けられたメッセージを受け取るだけです。 次に例を示します。

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET クライアントのログ記録

> [!WARNING]
> クライアント側のログには、アプリからの機密情報が含まれる場合があります。 運用アプリから GitHub などのパブリック フォーラムに未加工のログを投稿 **しないでください**。

.NET クライアントからログを取得するには、でメソッドを使用し `ConfigureLogging` `HubConnectionBuilder` ます。 これは、およびのメソッドと同じように動作し `ConfigureLogging` `WebHostBuilder` `HostBuilder` ます。 ASP.NET Core で使用するのと同じログプロバイダーを構成することができます。 ただし、個々のログプロバイダーに対して NuGet パッケージを手動でインストールして有効にする必要があります。

.NET クライアントのログ記録をアプリに追加するに Blazor WebAssembly は、「」を参照してください <xref:blazor/fundamentals/logging#signalr-net-client-logging> 。

### <a name="console-logging"></a>[コンソールのログ記録]

コンソールのログ記録を有効にするには [、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 追加します。 次に、メソッドを使用し `AddConsole` て、コンソールロガーを構成します。

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>デバッグ出力ウィンドウのログ記録

また、ログを構成して、Visual Studio の [ **出力** ] ウィンドウにアクセスすることもできます。 次のよう[に、パッケージを](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)インストールして、メソッドを使用します。 `AddDebug`

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>その他のログプロバイダー

SignalR では、Serilog、Seq、NLog などの他のログ記録プロバイダーや、と統合されるその他のログ記録プロバイダーがサポートされてい `Microsoft.Extensions.Logging` ます。 ログシステムにが用意されている場合は `ILoggerProvider` 、次のように登録でき `AddProvider` ます。

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>コントロールの詳細度

アプリ内の他の場所からログを記録している場合は、既定のレベルをに変更すると、詳細が表示されない場合 `Debug` があります。 フィルターを使用して、ログのログ記録レベルを構成でき SignalR ます。 これは、サーバーの場合とほぼ同じ方法でコードで行うことができます。

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>ネットワーク トレース

> [!WARNING]
> ネットワークトレースには、アプリによって送信されたすべてのメッセージの完全な内容が含まれています。 実稼働アプリから GitHub などのパブリックフォーラムに生のネットワークトレースを投稿 **しない** でください。

問題が発生した場合は、ネットワークトレースを使用すると、役に立つ情報が得られることがあります。 これは、問題の追跡ツールで問題をファイルする場合に特に便利です。

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Fiddler を使用してネットワークトレースを収集する (推奨オプション)

この方法は、すべてのアプリで使用できます。

Fiddler は、HTTP トレースを収集するための非常に強力なツールです。 [Telerik.com/fiddler](https://www.telerik.com/fiddler)からインストールして起動し、アプリを実行して問題を再現します。 Fiddler は Windows で使用でき、macOS と Linux のベータ版があります。

HTTPS を使用して接続する場合は、Fiddler が HTTPS トラフィックの暗号化を解除できるようにするための追加の手順がいくつかあります。 詳細については、 [Fiddler のドキュメント](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)を参照してください。

トレースを収集したら、[**ファイル**  >    >  ] メニューの [**すべてのセッション** を保存] をクリックして、トレースをエクスポートできます。

![Fiddler からすべてのセッションをエクスポートしています](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Tcpdump を使用してネットワークトレースを収集する (macOS および Linux のみ)

この方法は、すべてのアプリで使用できます。

コマンドシェルから次のコマンドを実行して、tcpdump を使用して未加工の TCP トレースを収集できます。 `root`アクセス許可のエラーが表示された場合は、コマンドにまたはのプレフィックスを付ける必要があり `sudo` ます。

```console
tcpdump -i [interface] -w trace.pcap
```

を `[interface]` キャプチャするネットワークインターフェイスで置き換えます。 通常は、 `/dev/eth0` (標準イーサネットインターフェイスの場合) または `/dev/lo0` (localhost トラフィックの場合) のようになります。 詳細については、 `tcpdump` ホストシステムの man ページを参照してください。

## <a name="collect-a-network-trace-in-the-browser"></a>ブラウザーでネットワーク トレースを収集する

この方法は、ブラウザーベースのアプリに対してのみ機能します。

ほとんどのブラウザーには、ブラウザーとサーバー間のネットワークアクティビティをキャプチャできる [ネットワーク] タブがあり開発者ツール。 ただし、これらのトレースには、WebSocket と Server-Sent イベントメッセージは含まれません。 これらのトランスポートを使用している場合は、Fiddler や TcpDump などのツールを使用することをお勧めします。

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge と Internet Explorer

(この手順は、Edge と Internet Explorer の両方で同じです)。

1. F12 キーを押して開発ツールを開きます。
2. [ネットワーク] タブをクリックします。
3. 必要に応じてページを更新し、問題を再現します
4. ツールバーの [保存] アイコンをクリックして、トレースを "HAR" ファイルとしてエクスポートします。

![Microsoft Edge Dev Tools の [ネットワーク] タブの [保存] アイコン](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. F12 キーを押して開発ツールを開きます。
2. [ネットワーク] タブをクリックします。
3. 必要に応じてページを更新し、問題を再現します
4. 要求の一覧の任意の場所を右クリックし、[コンテンツと共に HAR として保存] を選択します。

![Google Chrome Dev ツールの [ネットワーク] タブの [HAR として保存] オプション](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. F12 キーを押して開発ツールを開きます。
2. [ネットワーク] タブをクリックします。
3. 必要に応じてページを更新し、問題を再現します
4. 要求の一覧の任意の場所を右クリックし、[すべてを HAR として保存] を選択します。

![Mozilla Firefox Dev Tools [ネットワーク] タブの [すべてを HAR として保存] オプション](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>診断ファイルを GitHub の問題にアタッチする

GitHub の問題に診断ファイルを添付するには、名前を変更して拡張機能を設定し、 `.txt` 問題にドラッグアンドドロップします。

> [!NOTE]
> ログファイルやネットワークトレースの内容を GitHub の問題に貼り付けることは避けてください。 これらのログとトレースは非常に大きくなる可能性があり、GitHub は通常、これらを切り捨てます。

![GitHub の問題にログファイルをドラッグする](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a>メトリック

メトリックは、一定期間のデータ測定値を表します。 たとえば、1秒あたりの要求です。 メトリックデータを使用すると、アプリの状態を高レベルで監視できます。 .NET gRPC メトリックは <xref:System.Diagnostics.Tracing.EventCounter> を使用して出力されます。

### <a name="no-locsignalr-server-metrics"></a>SignalR サーバーメトリック

SignalR サーバーメトリックは、イベントソースで報告され <xref:Microsoft.AspNetCore.Http.Connections> ます。

| 名前                    | 説明                 |
|-------------------------|-----------------------------|
| `connections-started`   | 開始された接続の合計   |
| `connections-stopped`   | 停止した接続の合計数   |
| `connections-timed-out` | タイムアウトした接続の合計数 |
| `current-connections`   | 現在の接続数         |
| `connections-duration`  | 平均接続時間 |

### <a name="observe-metrics"></a>メトリックを観察する

[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) は、アドホックな正常性監視と最初のレベルのパフォーマンス調査を目的としたパフォーマンス監視ツールです。 プロバイダー名としてを使用して .NET アプリを監視し `Microsoft.AspNetCore.Http.Connections` ます。 たとえば、次のように入力します。

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a>その他のリソース

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
