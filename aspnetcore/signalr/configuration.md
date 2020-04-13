---
title: ASP.NETコアSignalR構成
author: bradygaster
description: ASP.NETコアSignalRアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223986"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR の構成

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/メッセージパックのシリアル化オプション

ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。 各プロトコルには、シリアル化構成オプションがあります。

JSON シリアル化は[、AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。 `AddJsonProtocol`の[後に追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)することができます`Startup.ConfigureServices`。 この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。 そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは`System.Text.Json`<xref:System.Text.Json.JsonSerializerOptions>、引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。 詳細については[、System.Text.Json ドキュメントを参照してください](/dotnet/api/system.text.json)。

たとえば、既定の "camelCase" 名ではなく、プロパティ名の大文字と小文字を変更しないようにシリアライザーを構成するには、 で`Startup.ConfigureServices`次のコードを使用します。

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。 拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> 現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。

### <a name="switch-to-newtonsoftjson"></a>ニュートンソフトに切り替える.

の機能`Newtonsoft.Json`が必要な場合は`System.Text.Json`、「 [Newtonsoft.Json に切り替える](xref:migration/22-to-30#switch-to-newtonsoftjson)」を参照してください。

### <a name="messagepack-serialization-options"></a>メッセージ パックのシリアル化オプション

メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。 詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバー オプションの構成

次の表に、SignalR ハブを設定するためのオプションを示します。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | この間隔でメッセージ (キープアライブを含む) を受信していない場合、サーバーはクライアントが切断されたと見なします。 クライアントが実際に切断としてマークされるには、このタイムアウト間隔よりも長い時間がかかる可能性があります。 推奨値は 2`KeepAliveInterval`倍の値です。|
| `HandshakeTimeout` | 15 秒 | クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。 を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。 推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。 |
| `EnableDetailedErrors` | `false` | 場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。 これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。 |
| `StreamBufferCapacity` | `10` | クライアント アップロード ストリーム用にバッファリングできる項目の最大数。 この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。|
| `MaximumReceiveMessageSize` | 32 KB | 単一の受信ハブ メッセージの最大サイズ。 |

オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>高度な HTTP 構成オプション

トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。 これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure`に渡すことによって構成されます。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | バックプレッシャを適用する前にサーバーがバッファリングするクライアントから受信した最大バイト数。 この値を大きくすると、バックプレッシャを適用せずに、サーバーがより大きなメッセージを受信する時間が増えますが、メモリ消費量が増加する可能性があります。 |
| `AuthorizationData` | ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | バックプレッシャを観察する前にサーバーがバッファーに入れるアプリによって送信される最大バイト数。 この値を大きくすると、サーバーはバックプレッシャを待たずに大きなメッセージをより迅速にバッファリングできますが、メモリ消費量を増加させることができます。 |
| `Transports` | すべてのトランスポートが有効になっています。 | クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。 |
| `LongPolling` | 以下を参照してください。 | ロングポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 以下を参照してください。 | WebSocket トランスポートに固有の追加オプション。 |

ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。 |

WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。 |

## <a name="configure-client-options"></a>クライアント オプションの構成

クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。 Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。

### <a name="configure-logging"></a>ログの構成

ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。 ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。 詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。

> [!NOTE]
> ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。 詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。

たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。 拡張メソッド`AddConsole`を呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。 生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。 ログは、ブラウザのコンソール ウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

値の`LogLevel`代わりに、ログ レベル名を`string`表す値を指定することもできます。 これは、定数にアクセスできない環境で SignalR ロギングを設定する場合に`LogLevel`便利です。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

次の表は、使用可能なログ レベルの一覧です。 指定する`configureLogging`値は、ログに記録される**最小**ログ レベルを設定します。 このレベルでログに記録されたメッセージ **、または表に示されたレベルの後に記録**されたメッセージがログに記録されます。

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**または**`information` | `LogLevel.Information` |
| `warn`**または**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。

ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。

SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。 これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。 次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視しても問題ありません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。 の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。 すべてのトランスポートは既定で有効になっています。

たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

このバージョンの Java クライアント Web ソケットは、使用可能なトランスポートのみです。

Java クライアントでは、トランスポートは、 の`withTransport`メソッドで選択されます`HttpHubConnectionBuilder`。 Java クライアントは、デフォルトで WebSockets トランスポートを使用します。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> SignalR Java クライアントは、まだトランスポート フォールバックをサポートしていません。

### <a name="configure-bearer-authentication"></a>ベアラ認証の構成

SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。 NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。 JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。 このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。

NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。 を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。 [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブ オプションを構成する

`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。

# <a name="net"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |
| `HandshakeTimeout` | 15 秒 | 最初のサーバー ハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。 クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。 |

NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |
| `keepAliveIntervalInMilliseconds` | 15 秒 (15,000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。 クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。 |

# <a name="java"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |
| `withHandshakeResponseTimeout` | 15 秒 | 最初のサーバー ハンドシェイクのタイムアウト。 この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 秒 (15,000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。 クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。 |

---

### <a name="configure-additional-options"></a>詳細設定オプションの構成

追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | 説明 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | すべての HTTP 要求で送信する HTTP クッキーのコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求で送信する資格情報。 |
| `CloseTimeout` | 5 秒 | ウェブソケットのみ。 サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。 この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。 Web ソケット接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。 その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。 **ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Java スクリプトオプション | Default value | 説明 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `skipNegotiation` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |

# <a name="java"></a>[Java](#tab/java)

| Java オプション | Default value | 説明 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。 |

---

NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/メッセージパックのシリアル化オプション

ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。 各プロトコルには、シリアル化構成オプションがあります。

JSON シリアル化は、メソッドの[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できる[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを`Startup.ConfigureServices`使用してサーバー上で構成できます。 この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。 そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、`JsonSerializerSettings`引数と戻り値のシリアル化を構成するために使用できるJSON.NETオブジェクトです。 詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。
 
たとえば、既定の "camelCase" の名前ではなく、"PascalCase" プロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。 拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> 現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。

### <a name="messagepack-serialization-options"></a>メッセージ パックのシリアル化オプション

メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。 詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバー オプションの構成

次の表に、SignalR ハブを設定するためのオプションを示します。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | この間隔でメッセージ (キープアライブを含む) を受信していない場合、サーバーはクライアントが切断されたと見なします。 クライアントが実際に切断としてマークされるには、このタイムアウト間隔よりも長い時間がかかる可能性があります。 推奨値は 2`KeepAliveInterval`倍の値です。|
| `HandshakeTimeout` | 15 秒 | クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。 を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。 推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。 |
| `EnableDetailedErrors` | `false` | 場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。 これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。 |

オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>高度な HTTP 構成オプション

トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。 これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure`に渡すことによって構成されます。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | サーバーがバッファリングするクライアントから受信した最大バイト数。 この値を大きくすると、サーバーはより大きなメッセージを受信できますが、メモリ消費に悪影響を及ぼす可能性があります。 |
| `AuthorizationData` | ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバッファーに格納するアプリによって送信される最大バイト数。 この値を大きくすると、サーバーは大きなメッセージを送信できますが、メモリ消費に悪影響を及ぼす可能性があります。 |
| `Transports` | すべてのトランスポートが有効になっています。 | クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。 |
| `LongPolling` | 以下を参照してください。 | ロングポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 以下を参照してください。 | WebSocket トランスポートに固有の追加オプション。 |

ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。 |

WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。 |

## <a name="configure-client-options"></a>クライアント オプションの構成

クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。 Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。

### <a name="configure-logging"></a>ログの構成

ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。 ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。 詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。

> [!NOTE]
> ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。 詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。

たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。 拡張メソッド`AddConsole`を呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。 生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。 ログは、ブラウザのコンソール ウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。

ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。

SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。 これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。 次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視しても問題ありません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。 の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。 すべてのトランスポートは既定で有効になっています。

たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

このバージョンの Java クライアント Web ソケットは、使用可能なトランスポートのみです。

### <a name="configure-bearer-authentication"></a>ベアラ認証の構成

SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。 NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。 JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。 このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。

NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。 を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。 [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブ オプションを構成する

`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。

# <a name="net"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |
| `HandshakeTimeout` | 15 秒 | 最初のサーバー ハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。 クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。 |

NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |
| `keepAliveIntervalInMilliseconds` | 15 秒 (15,000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。 クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。 |

# <a name="java"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |
| `withHandshakeResponseTimeout` | 15 秒 | 最初のサーバー ハンドシェイクのタイムアウト。 この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 秒 (15,000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。 クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。 |

---

### <a name="configure-additional-options"></a>詳細設定オプションの構成

追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | 説明 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | すべての HTTP 要求で送信する HTTP クッキーのコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求で送信する資格情報。 |
| `CloseTimeout` | 5 秒 | ウェブソケットのみ。 サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。 この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。 Web ソケット接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。 その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。 **ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Java スクリプトオプション | Default value | 説明 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `skipNegotiation` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |

# <a name="java"></a>[Java](#tab/java)

| Java オプション | Default value | 説明 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。 |

---

NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/メッセージパックのシリアル化オプション

ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。 各プロトコルには、シリアル化構成オプションがあります。

JSON シリアル化は、メソッドの[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できる[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを`Startup.ConfigureServices`使用してサーバー上で構成できます。 この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。 そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、`JsonSerializerSettings`引数と戻り値のシリアル化を構成するために使用できるJSON.NETオブジェクトです。 詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。
 
たとえば、既定の "camelCase" の名前ではなく、"PascalCase" プロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。 拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> 現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。

### <a name="messagepack-serialization-options"></a>メッセージ パックのシリアル化オプション

メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。 詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバー オプションの構成

次の表に、SignalR ハブを設定するためのオプションを示します。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 秒 | クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。 を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。 推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。 |
| `EnableDetailedErrors` | `false` | 場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。 これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。 |

オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>高度な HTTP 構成オプション

トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。 これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure`に渡すことによって構成されます。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | サーバーがバッファリングするクライアントから受信した最大バイト数。 この値を大きくすると、サーバーはより大きなメッセージを受信できますが、メモリ消費に悪影響を及ぼす可能性があります。 |
| `AuthorizationData` | ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバッファーに格納するアプリによって送信される最大バイト数。 この値を大きくすると、サーバーは大きなメッセージを送信できますが、メモリ消費に悪影響を及ぼす可能性があります。 |
| `Transports` | すべてのトランスポートが有効になっています。 | クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。 |
| `LongPolling` | 以下を参照してください。 | ロングポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 以下を参照してください。 | WebSocket トランスポートに固有の追加オプション。 |

ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。 |

WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。

| オプション | Default value | 説明 |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。 |

## <a name="configure-client-options"></a>クライアント オプションの構成

クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。 Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。

### <a name="configure-logging"></a>ログの構成

ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。 ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。 詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。

> [!NOTE]
> ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。 詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。

たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。 拡張メソッド`AddConsole`を呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。 生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。 ログは、ブラウザのコンソール ウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。

ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。

SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。 これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。 次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視しても問題ありません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。 の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。 すべてのトランスポートは既定で有効になっています。

たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>ベアラ認証の構成

SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。 NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。 JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。 このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。

NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。 を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。 [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブ オプションを構成する

`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。

# <a name="net"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |
| `HandshakeTimeout` | 15 秒 | 最初のサーバー ハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |

NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |

# <a name="java"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 秒 (30,000 ミリ秒) | サーバーアクティビティのタイムアウト。 この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。 この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。 推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。 |
| `withHandshakeResponseTimeout` | 15 秒 | 最初のサーバー ハンドシェイクのタイムアウト。 この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。 これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。 ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。 |

---

### <a name="configure-additional-options"></a>詳細設定オプションの構成

追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | 説明 |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | すべての HTTP 要求で送信する HTTP クッキーのコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求で送信する資格情報。 |
| `CloseTimeout` | 5 秒 | ウェブソケットのみ。 サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。 この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。 Web ソケット接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。 その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。 **ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Java スクリプトオプション | Default value | 説明 |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `skipNegotiation` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |

# <a name="java"></a>[Java](#tab/java)

| Java オプション | Default value | 説明 |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションステップを`true`スキップするには、これをに設定します。 **WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。 Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。 |

---

NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
