---
title: ASP.NET Core SignalR の構成
author: bradygaster
description: ASP.NET Core アプリを構成する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 7dac8c84683553a52e07ecc61c8bcf8616e77dc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061237"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a>ASP.NET Core SignalR の構成

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack のシリアル化オプション

ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。 各プロトコルには、シリアル化の構成オプションがあります。

JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。 `AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。 メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。 そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。 詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。

たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。 `Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。

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
> 現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft.Jsに切り替えます

`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。

### <a name="messagepack-serialization-options"></a>MessagePack のシリアル化オプション

MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。 詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバーオプションの構成

次の表では、ハブを構成するためのオプションについて説明し SignalR ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。 クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。 推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。|
| `HandshakeTimeout` | 15 秒 | この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。 変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。 推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。 |
| `EnableDetailedErrors` | `false` | `true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。 |
| `StreamBufferCapacity` | `10` | クライアントアップロードストリームに対してバッファーできる項目の最大数。 この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。|
| `MaximumReceiveMessageSize` | 32 KB | 1つの受信ハブメッセージの最大サイズ。 |
| `MaximumParallelInvocationsPerClient` | 1 | 各クライアントがキューに入れる前に並列で呼び出すことができるハブメソッドの最大数。 |

オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。

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

1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>詳細な HTTP 構成オプション

`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。 これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。 この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。 |
| `AuthorizationData` | `Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。 この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。 |
| `Transports` | すべてのトランスポートが有効になります。 | `HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。 |
| `LongPolling` | 次を参照してください。 | 長いポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 次を参照してください。 | Websocket トランスポートに固有の追加オプション。 |
| `MinimumProtocolVersion` | 0 | Negotiate プロトコルの最小バージョンを指定します。 これは、クライアントを新しいバージョンに制限するために使用されます。 |

長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。 |

WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。 |

## <a name="configure-client-options"></a>クライアント オプションを構成する

クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。 Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。

### <a name="configure-logging"></a>ログの構成

ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。 ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。 詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。

> [!NOTE]
> ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。 完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。

たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。 `AddConsole`拡張メソッドを呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。 `LogLevel`生成するログメッセージの最小レベルを示す値を指定します。 ログは、ブラウザーのコンソールウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。 これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

次の表は、使用可能なログレベルを示しています。 `configureLogging`ログに記録される **最小** ログレベルを設定するために指定する値。 このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの** メッセージはログに記録されます。

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
> 完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。

ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。

SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。 これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。 次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視してもかまいません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。 の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。 既定では、すべてのトランスポートが有効になっています。

たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。

Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。 Java クライアントでは、既定で Websocket トランスポートが使用されます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。

### <a name="configure-bearer-authentication"></a>ベアラー認証を構成する

要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。 .NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。 JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。 このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。

.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。 [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。 [単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブオプションを構成する

タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `HandshakeTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

# <a name="java"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

---

### <a name="configure-additional-options"></a>詳細設定オプションの構成

追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | [説明] |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | cookieすべての http 要求と共に送信する http のコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求と共に送信する資格情報。 |
| `CloseTimeout` | 5 秒 | Websocket のみ。 サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。 この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | `HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。 WebSocket 接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。 既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。 **ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript オプション | 既定値 | [説明] |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。 |
| `headers` | `null` | すべての HTTP 要求と共に送信されるヘッダーのディクショナリ。 ブラウザーでヘッダーを送信することは、Websocket やストリームでは機能しません <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。 |
| `logMessageContent` | `null` | `true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。 |
| `skipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `withCredentials` | `true` | CORS 要求と共に資格情報を送信するかどうかを指定します。 Azure App Service は cookie 固定セッションでを使用します。このオプションを正しく動作させるには、このオプションを有効にする必要があります。 での CORS の詳細につい SignalR ては、「」を参照してください <xref:signalr/security#cross-origin-resource-sharing> 。 |

# <a name="java"></a>[Java](#tab/java)

| Java オプション | 既定値 | [説明] |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |

---

.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他の資料

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack のシリアル化オプション

ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。 各プロトコルには、シリアル化の構成オプションがあります。

JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。 `AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。 メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。 そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。 詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。

たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。 `Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。

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
> 現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft.Jsに切り替えます

`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。

### <a name="messagepack-serialization-options"></a>MessagePack のシリアル化オプション

MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。 詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバーオプションの構成

次の表では、ハブを構成するためのオプションについて説明し SignalR ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。 クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。 推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。|
| `HandshakeTimeout` | 15 秒 | この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。 変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。 推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。 |
| `EnableDetailedErrors` | `false` | `true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。 |
| `StreamBufferCapacity` | `10` | クライアントアップロードストリームに対してバッファーできる項目の最大数。 この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。|
| `MaximumReceiveMessageSize` | 32 KB | 1つの受信ハブメッセージの最大サイズ。 |

オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。

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

1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>詳細な HTTP 構成オプション

`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。 これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。 この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。 |
| `AuthorizationData` | `Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。 この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。 |
| `Transports` | すべてのトランスポートが有効になります。 | `HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。 |
| `LongPolling` | 次を参照してください。 | 長いポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 次を参照してください。 | Websocket トランスポートに固有の追加オプション。 |
| `MinimumProtocolVersion` | 0 | Negotiate プロトコルの最小バージョンを指定します。 これは、クライアントを新しいバージョンに制限するために使用されます。 |

長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。 |

WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。 |

## <a name="configure-client-options"></a>クライアント オプションを構成する

クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。 Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。

### <a name="configure-logging"></a>ログの構成

ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。 ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。 詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。

> [!NOTE]
> ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。 完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。

たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。 `AddConsole`拡張メソッドを呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。 `LogLevel`生成するログメッセージの最小レベルを示す値を指定します。 ログは、ブラウザーのコンソールウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。 これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

次の表は、使用可能なログレベルを示しています。 `configureLogging`ログに記録される **最小** ログレベルを設定するために指定する値。 このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの** メッセージはログに記録されます。

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
> 完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。

ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。

SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。 これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。 次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視してもかまいません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。 の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。 既定では、すべてのトランスポートが有効になっています。

たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。

Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。 Java クライアントでは、既定で Websocket トランスポートが使用されます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。

### <a name="configure-bearer-authentication"></a>ベアラー認証を構成する

要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。 .NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。 JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。 このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。

.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。 [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。 [単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブオプションを構成する

タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `HandshakeTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

# <a name="java"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

---

### <a name="configure-additional-options"></a>詳細設定オプションの構成

追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | [説明] |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | cookieすべての http 要求と共に送信する http のコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求と共に送信する資格情報。 |
| `CloseTimeout` | 5 秒 | Websocket のみ。 サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。 この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | `HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。 WebSocket 接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。 既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。 **ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript オプション | 既定値 | [説明] |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。 |
| `logMessageContent` | `null` | `true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。 |
| `skipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |

# <a name="java"></a>[Java](#tab/java)

| Java オプション | 既定値 | [説明] |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |

---

.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他の資料

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack のシリアル化オプション

ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。 各プロトコルには、シリアル化の構成オプションがあります。

JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。 `AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。 メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。 そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。 詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。

たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。 `Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。

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
> 現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。

### <a name="switch-to-newtonsoftjson"></a>Newtonsoft.Jsに切り替えます

`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。

### <a name="messagepack-serialization-options"></a>MessagePack のシリアル化オプション

MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。 詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバーオプションの構成

次の表では、ハブを構成するためのオプションについて説明し SignalR ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。 クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。 推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。|
| `HandshakeTimeout` | 15 秒 | この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。 変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。 推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。 |
| `EnableDetailedErrors` | `false` | `true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。 |
| `StreamBufferCapacity` | `10` | クライアントアップロードストリームに対してバッファーできる項目の最大数。 この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。|
| `MaximumReceiveMessageSize` | 32 KB | 1つの受信ハブメッセージの最大サイズ。 |

オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。

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

1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>詳細な HTTP 構成オプション

`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。 これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。 この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。 |
| `AuthorizationData` | `Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。 この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。 |
| `Transports` | すべてのトランスポートが有効になります。 | `HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。 |
| `LongPolling` | 次を参照してください。 | 長いポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 次を参照してください。 | Websocket トランスポートに固有の追加オプション。 |

長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。 |

WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。 |

## <a name="configure-client-options"></a>クライアント オプションを構成する

クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。 Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。

### <a name="configure-logging"></a>ログの構成

ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。 ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。 詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。

> [!NOTE]
> ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。 完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。

たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。 `AddConsole`拡張メソッドを呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。 `LogLevel`生成するログメッセージの最小レベルを示す値を指定します。 ログは、ブラウザーのコンソールウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。 これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

次の表は、使用可能なログレベルを示しています。 `configureLogging`ログに記録される **最小** ログレベルを設定するために指定する値。 このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの** メッセージはログに記録されます。

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
> 完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。

ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。

SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。 これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。 次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視してもかまいません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。 の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。 既定では、すべてのトランスポートが有効になっています。

たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。

Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。 Java クライアントでは、既定で Websocket トランスポートが使用されます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。

### <a name="configure-bearer-authentication"></a>ベアラー認証を構成する

要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。 .NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。 JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。 このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。

.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。 [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。 [単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブオプションを構成する

タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `HandshakeTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

# <a name="java"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

---

### <a name="configure-additional-options"></a>詳細設定オプションの構成

追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | [説明] |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | cookieすべての http 要求と共に送信する http のコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求と共に送信する資格情報。 |
| `CloseTimeout` | 5 秒 | Websocket のみ。 サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。 この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | `HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。 WebSocket 接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。 既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。 **ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript オプション | 既定値 | [説明] |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。 |
| `logMessageContent` | `null` | `true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。 |
| `skipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |

# <a name="java"></a>[Java](#tab/java)

| Java オプション | 既定値 | [説明] |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |

---

.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他の資料

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack のシリアル化オプション

ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。 各プロトコルには、シリアル化の構成オプションがあります。

JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドに[Add を SignalR 追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)した後に追加でき `Startup.ConfigureServices` ます。 メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。 そのオブジェクトの [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。 詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。
 
たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。 `Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。

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
> 現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。

### <a name="messagepack-serialization-options"></a>MessagePack のシリアル化オプション

MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。 詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバーオプションの構成

次の表では、ハブを構成するためのオプションについて説明し SignalR ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 秒 | サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。 クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。 推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。|
| `HandshakeTimeout` | 15 秒 | この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。 変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。 推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。 |
| `EnableDetailedErrors` | `false` | `true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。 |

オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。

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

1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>詳細な HTTP 構成オプション

`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。 これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | クライアントから受信した、サーバーがバッファーする最大バイト数。 この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。 |
| `AuthorizationData` | `Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバッファーするアプリによって送信される最大バイト数。 この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。 |
| `Transports` | すべてのトランスポートが有効になります。 | `HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。 |
| `LongPolling` | 次を参照してください。 | 長いポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 次を参照してください。 | Websocket トランスポートに固有の追加オプション。 |

長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。 |

WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。 |

## <a name="configure-client-options"></a>クライアント オプションを構成する

クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。 Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。

### <a name="configure-logging"></a>ログの構成

ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。 ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。 詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。

> [!NOTE]
> ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。 完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。

たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。 `AddConsole`拡張メソッドを呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。 `LogLevel`生成するログメッセージの最小レベルを示す値を指定します。 ログは、ブラウザーのコンソールウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。

ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。

SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。 これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。 次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視してもかまいません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。 の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。 既定では、すべてのトランスポートが有効になっています。

たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。

### <a name="configure-bearer-authentication"></a>ベアラー認証を構成する

要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。 .NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。 JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。 このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。

.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。 [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。 [単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブオプションを構成する

タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `HandshakeTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `keepAliveIntervalInMilliseconds` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

# <a name="java"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15秒 (15000 ミリ秒) | クライアントが ping メッセージを送信する間隔を決定します。 クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。 クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。 |

---

### <a name="configure-additional-options"></a>詳細設定オプションの構成

追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | [説明] |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | cookieすべての http 要求と共に送信する http のコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求と共に送信する資格情報。 |
| `CloseTimeout` | 5 秒 | Websocket のみ。 サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。 この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | `HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。 WebSocket 接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。 既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。 **ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript オプション | 既定値 | [説明] |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。 |
| `logMessageContent` | `null` | `true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。 |
| `skipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |

# <a name="java"></a>[Java](#tab/java)

| Java オプション | 既定値 | [説明] |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |

---

.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他の資料

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack のシリアル化オプション

ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。 各プロトコルには、シリアル化の構成オプションがあります。

JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドに[Add を SignalR 追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)した後に追加でき `Startup.ConfigureServices` ます。 メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。 そのオブジェクトの [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。 詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。
 
たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。 `Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。

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
> 現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。

### <a name="messagepack-serialization-options"></a>MessagePack のシリアル化オプション

MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。 詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。

> [!NOTE]
> 現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。

## <a name="configure-server-options"></a>サーバーオプションの構成

次の表では、ハブを構成するためのオプションについて説明し SignalR ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 秒 | この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |
| `KeepAliveInterval` | 15 秒 | サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。 変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。 推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。  |
| `SupportedProtocols` | インストールされているすべてのプロトコル | このハブでサポートされているプロトコル。 既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。 |
| `EnableDetailedErrors` | `false` | `true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。 既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。 |

オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。

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

1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>詳細な HTTP 構成オプション

`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。 これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | クライアントから受信した、サーバーがバッファーする最大バイト数。 この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。 |
| `AuthorizationData` | `Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。 | クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。 |
| `TransportMaxBufferSize` | 32 KB | サーバーがバッファーするアプリによって送信される最大バイト数。 この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。 |
| `Transports` | すべてのトランスポートが有効になります。 | `HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。 |
| `LongPolling` | 次を参照してください。 | 長いポーリングトランスポートに固有の追加オプション。 |
| `WebSockets` | 次を参照してください。 | Websocket トランスポートに固有の追加オプション。 |

長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90秒 | 1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。 この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。 |

WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 秒 | サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。 |
| `SubProtocolSelector` | `null` | `Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。 デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。 |

## <a name="configure-client-options"></a>クライアント オプションを構成する

クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。 Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。

### <a name="configure-logging"></a>ログの構成

ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。 ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。 詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。

> [!NOTE]
> ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。 完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。

たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。 `AddConsole`拡張メソッドを呼び出します。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。 `LogLevel`生成するログメッセージの最小レベルを示す値を指定します。 ログは、ブラウザーのコンソールウィンドウに書き込まれます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> 完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。

ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。

SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。 これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。 次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視してもかまいません。

### <a name="configure-allowed-transports"></a>許可されたトランスポートの構成

によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。 の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。 既定では、すべてのトランスポートが有効になっています。

たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>ベアラー認証を構成する

要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。 .NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。 JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。 このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。

.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。 [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。 [単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>タイムアウトとキープアライブオプションを構成する

タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |
| `HandshakeTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |

.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| オプション | 既定値 | [説明] |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。 |

# <a name="java"></a>[Java](#tab/java)

| オプション | 既定値 | 説明 |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30秒 (3万ミリ秒) | サーバーの利用状況のタイムアウト。 サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。 この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。 推奨値は、サーバーの値の少なくとも2倍の数で、 `KeepAliveInterval` ping が到着するまでの時間を確保します。 |
| `withHandshakeResponseTimeout` | 15 秒 | 初期サーバーハンドシェイクのタイムアウト。 サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。 これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。 ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。 |

---

### <a name="configure-additional-options"></a>詳細設定オプションの構成

追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET オプション |  既定値 | [説明] |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `SkipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `ClientCertificates` | Empty | 認証要求に送信する TLS 証明書のコレクション。 |
| `Cookies` | Empty | cookieすべての http 要求と共に送信する http のコレクション。 |
| `Credentials` | Empty | すべての HTTP 要求と共に送信する資格情報。 |
| `CloseTimeout` | 5 秒 | Websocket のみ。 サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。 この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。 |
| `Headers` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |
| `HttpMessageHandlerFactory` | `null` | `HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。 WebSocket 接続には使用されません。 このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。 既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。 **ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。** |
| `Proxy` | `null` | HTTP 要求を送信するときに使用する HTTP プロキシ。 |
| `UseDefaultCredentials` | `false` | このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。 これにより、Windows 認証を使用できるようになります。 |
| `WebSocketConfiguration` | `null` | 追加の WebSocket オプションを構成するために使用できるデリゲート。 オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。 |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript オプション | 既定値 | [説明] |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `transport` | `null` | <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。 |
| `logMessageContent` | `null` | `true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。 |
| `skipNegotiation` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |

# <a name="java"></a>[Java](#tab/java)

| Java オプション | 既定値 | [説明] |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。 |
| `shouldSkipNegotiate` | `false` | ネゴシエーションの手順をスキップするには、これをに設定 `true` します。 **Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。 Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。 |
| `withHeader` `withHeaders` | Empty | すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。 |

---

.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>その他のリソース

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
