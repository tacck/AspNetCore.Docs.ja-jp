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
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="13d49-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="13d49-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="13d49-104">JSON/メッセージパックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="13d49-105">ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="13d49-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="13d49-106">各プロトコルには、シリアル化構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="13d49-107">JSON シリアル化は[、AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="13d49-108">`AddJsonProtocol`の[後に追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)することができます`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="13d49-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="13d49-109">この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="13d49-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="13d49-110">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは`System.Text.Json`<xref:System.Text.Json.JsonSerializerOptions>、引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="13d49-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="13d49-111">詳細については[、System.Text.Json ドキュメントを参照してください](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="13d49-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="13d49-112">たとえば、既定の "camelCase" 名ではなく、プロパティ名の大文字と小文字を変更しないようにシリアライザーを構成するには、 で`Startup.ConfigureServices`次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="13d49-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="13d49-113">NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。</span><span class="sxs-lookup"><span data-stu-id="13d49-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="13d49-114">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="13d49-115">現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="13d49-116">ニュートンソフトに切り替える.</span><span class="sxs-lookup"><span data-stu-id="13d49-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="13d49-117">の機能`Newtonsoft.Json`が必要な場合は`System.Text.Json`、「 [Newtonsoft.Json に切り替える](xref:migration/22-to-30#switch-to-newtonsoftjson)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="13d49-118">メッセージ パックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-118">MessagePack serialization options</span></span>

<span data-ttu-id="13d49-119">メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="13d49-120">詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="13d49-121">現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="13d49-122">サーバー オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-122">Configure server options</span></span>

<span data-ttu-id="13d49-123">次の表に、SignalR ハブを設定するためのオプションを示します。</span><span class="sxs-lookup"><span data-stu-id="13d49-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="13d49-124">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-124">Option</span></span> | <span data-ttu-id="13d49-125">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-125">Default Value</span></span> | <span data-ttu-id="13d49-126">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="13d49-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-127">30 seconds</span></span> | <span data-ttu-id="13d49-128">この間隔でメッセージ (キープアライブを含む) を受信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="13d49-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="13d49-129">クライアントが実際に切断としてマークされるには、このタイムアウト間隔よりも長い時間がかかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="13d49-130">推奨値は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="13d49-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-131">15 seconds</span></span> | <span data-ttu-id="13d49-132">クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="13d49-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="13d49-133">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-134">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="13d49-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-135">15 seconds</span></span> | <span data-ttu-id="13d49-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="13d49-137">を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="13d49-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="13d49-138">推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="13d49-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="13d49-139">All installed protocols</span></span> | <span data-ttu-id="13d49-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="13d49-140">Protocols supported by this hub.</span></span> <span data-ttu-id="13d49-141">既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13d49-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="13d49-142">場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="13d49-143">これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="13d49-144">クライアント アップロード ストリーム用にバッファリングできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="13d49-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="13d49-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="13d49-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="13d49-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="13d49-146">32 KB</span></span> | <span data-ttu-id="13d49-147">単一の受信ハブ メッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="13d49-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="13d49-148">オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="13d49-149">単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="13d49-150">高度な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="13d49-151">トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="13d49-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="13d49-152">これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure`に渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="13d49-153">次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="13d49-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="13d49-154">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-154">Option</span></span> | <span data-ttu-id="13d49-155">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-155">Default Value</span></span> | <span data-ttu-id="13d49-156">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="13d49-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="13d49-157">32 KB</span></span> | <span data-ttu-id="13d49-158">バックプレッシャを適用する前にサーバーがバッファリングするクライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="13d49-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="13d49-159">この値を大きくすると、バックプレッシャを適用せずに、サーバーがより大きなメッセージを受信する時間が増えますが、メモリ消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="13d49-160">ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="13d49-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="13d49-161">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="13d49-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="13d49-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="13d49-162">32 KB</span></span> | <span data-ttu-id="13d49-163">バックプレッシャを観察する前にサーバーがバッファーに入れるアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="13d49-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="13d49-164">この値を大きくすると、サーバーはバックプレッシャを待たずに大きなメッセージをより迅速にバッファリングできますが、メモリ消費量を増加させることができます。</span><span class="sxs-lookup"><span data-stu-id="13d49-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="13d49-165">すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="13d49-165">All Transports are enabled.</span></span> | <span data-ttu-id="13d49-166">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。</span><span class="sxs-lookup"><span data-stu-id="13d49-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="13d49-167">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-167">See below.</span></span> | <span data-ttu-id="13d49-168">ロングポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="13d49-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="13d49-169">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-169">See below.</span></span> | <span data-ttu-id="13d49-170">WebSocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="13d49-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="13d49-171">ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="13d49-172">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-172">Option</span></span> | <span data-ttu-id="13d49-173">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-173">Default Value</span></span> | <span data-ttu-id="13d49-174">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="13d49-175">90秒</span><span class="sxs-lookup"><span data-stu-id="13d49-175">90 seconds</span></span> | <span data-ttu-id="13d49-176">1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="13d49-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="13d49-177">この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="13d49-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="13d49-178">WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="13d49-179">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-179">Option</span></span> | <span data-ttu-id="13d49-180">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-180">Default Value</span></span> | <span data-ttu-id="13d49-181">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="13d49-182">5 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-182">5 seconds</span></span> | <span data-ttu-id="13d49-183">サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="13d49-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="13d49-184">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="13d49-185">デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="13d49-186">クライアント オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-186">Configure client options</span></span>

<span data-ttu-id="13d49-187">クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。</span><span class="sxs-lookup"><span data-stu-id="13d49-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="13d49-188">Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。</span><span class="sxs-lookup"><span data-stu-id="13d49-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="13d49-189">ログの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-189">Configure logging</span></span>

<span data-ttu-id="13d49-190">ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="13d49-191">ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="13d49-192">詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="13d49-193">ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="13d49-194">詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="13d49-195">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。</span><span class="sxs-lookup"><span data-stu-id="13d49-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="13d49-196">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13d49-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="13d49-197">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="13d49-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="13d49-198">生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="13d49-199">ログは、ブラウザのコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="13d49-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="13d49-200">値の`LogLevel`代わりに、ログ レベル名を`string`表す値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="13d49-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="13d49-201">これは、定数にアクセスできない環境で SignalR ロギングを設定する場合に`LogLevel`便利です。</span><span class="sxs-lookup"><span data-stu-id="13d49-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="13d49-202">次の表は、使用可能なログ レベルの一覧です。</span><span class="sxs-lookup"><span data-stu-id="13d49-202">The following table lists the available log levels.</span></span> <span data-ttu-id="13d49-203">指定する`configureLogging`値は、ログに記録される**最小**ログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="13d49-204">このレベルでログに記録されたメッセージ **、または表に示されたレベルの後に記録**されたメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="13d49-205">String</span><span class="sxs-lookup"><span data-stu-id="13d49-205">String</span></span>                      | <span data-ttu-id="13d49-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="13d49-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="13d49-207">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="13d49-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="13d49-208">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="13d49-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="13d49-209">ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="13d49-210">ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="13d49-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="13d49-211">SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。</span><span class="sxs-lookup"><span data-stu-id="13d49-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="13d49-212">これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。</span><span class="sxs-lookup"><span data-stu-id="13d49-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="13d49-213">次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。</span><span class="sxs-lookup"><span data-stu-id="13d49-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="13d49-214">依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="13d49-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="13d49-215">これは無視しても問題ありません。</span><span class="sxs-lookup"><span data-stu-id="13d49-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="13d49-216">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-216">Configure allowed transports</span></span>

<span data-ttu-id="13d49-217">SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="13d49-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="13d49-218">の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="13d49-219">すべてのトランスポートは既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="13d49-219">All transports are enabled by default.</span></span>

<span data-ttu-id="13d49-220">たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="13d49-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="13d49-221">JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="13d49-222">このバージョンの Java クライアント Web ソケットは、使用可能なトランスポートのみです。</span><span class="sxs-lookup"><span data-stu-id="13d49-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="13d49-223">Java クライアントでは、トランスポートは、 の`withTransport`メソッドで選択されます`HttpHubConnectionBuilder`。</span><span class="sxs-lookup"><span data-stu-id="13d49-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="13d49-224">Java クライアントは、デフォルトで WebSockets トランスポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="13d49-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="13d49-225">SignalR Java クライアントは、まだトランスポート フォールバックをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="13d49-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="13d49-226">ベアラ認証の構成</span><span class="sxs-lookup"><span data-stu-id="13d49-226">Configure bearer authentication</span></span>

<span data-ttu-id="13d49-227">SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="13d49-228">NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。</span><span class="sxs-lookup"><span data-stu-id="13d49-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="13d49-229">JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="13d49-230">このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="13d49-231">NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="13d49-232">JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。</span><span class="sxs-lookup"><span data-stu-id="13d49-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="13d49-233">SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="13d49-234">を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。</span><span class="sxs-lookup"><span data-stu-id="13d49-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="13d49-235">[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="13d49-236">タイムアウトとキープアライブ オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13d49-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="13d49-237">`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="13d49-238">.NET</span><span class="sxs-lookup"><span data-stu-id="13d49-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="13d49-239">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-239">Option</span></span> | <span data-ttu-id="13d49-240">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-240">Default value</span></span> | <span data-ttu-id="13d49-241">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="13d49-242">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-243">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-243">Timeout for server activity.</span></span> <span data-ttu-id="13d49-244">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="13d49-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="13d49-245">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-246">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="13d49-247">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-247">15 seconds</span></span> | <span data-ttu-id="13d49-248">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="13d49-249">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="13d49-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="13d49-250">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-251">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="13d49-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-252">15 seconds</span></span> | <span data-ttu-id="13d49-253">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="13d49-254">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="13d49-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="13d49-255">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="13d49-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="13d49-256">NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="13d49-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="13d49-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="13d49-258">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-258">Option</span></span> | <span data-ttu-id="13d49-259">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-259">Default value</span></span> | <span data-ttu-id="13d49-260">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="13d49-261">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-262">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-262">Timeout for server activity.</span></span> <span data-ttu-id="13d49-263">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="13d49-264">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-265">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="13d49-266">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="13d49-267">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="13d49-268">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="13d49-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="13d49-269">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="13d49-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="13d49-270">Java</span><span class="sxs-lookup"><span data-stu-id="13d49-270">Java</span></span>](#tab/java)

| <span data-ttu-id="13d49-271">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-271">Option</span></span> | <span data-ttu-id="13d49-272">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-272">Default value</span></span> | <span data-ttu-id="13d49-273">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="13d49-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="13d49-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="13d49-275">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-276">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-276">Timeout for server activity.</span></span> <span data-ttu-id="13d49-277">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="13d49-278">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-279">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="13d49-280">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-280">15 seconds</span></span> | <span data-ttu-id="13d49-281">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="13d49-282">この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="13d49-283">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-284">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="13d49-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="13d49-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="13d49-286">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="13d49-287">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="13d49-288">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="13d49-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="13d49-289">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="13d49-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="13d49-290">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-290">Configure additional options</span></span>

<span data-ttu-id="13d49-291">追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="13d49-292">.NET</span><span class="sxs-lookup"><span data-stu-id="13d49-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="13d49-293">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-293">.NET Option</span></span> |  <span data-ttu-id="13d49-294">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-294">Default value</span></span> | <span data-ttu-id="13d49-295">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="13d49-296">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="13d49-297">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-298">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-299">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="13d49-300">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-300">Empty</span></span> | <span data-ttu-id="13d49-301">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="13d49-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="13d49-302">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-302">Empty</span></span> | <span data-ttu-id="13d49-303">すべての HTTP 要求で送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="13d49-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="13d49-304">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-304">Empty</span></span> | <span data-ttu-id="13d49-305">すべての HTTP 要求で送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="13d49-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="13d49-306">5 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-306">5 seconds</span></span> | <span data-ttu-id="13d49-307">ウェブソケットのみ。</span><span class="sxs-lookup"><span data-stu-id="13d49-307">WebSockets only.</span></span> <span data-ttu-id="13d49-308">サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="13d49-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="13d49-309">この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="13d49-310">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-310">Empty</span></span> | <span data-ttu-id="13d49-311">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="13d49-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="13d49-312">HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="13d49-313">Web ソケット接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="13d49-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="13d49-314">このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。</span><span class="sxs-lookup"><span data-stu-id="13d49-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="13d49-315">その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="13d49-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="13d49-316">**ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。**</span><span class="sxs-lookup"><span data-stu-id="13d49-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="13d49-317">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="13d49-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="13d49-318">HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="13d49-319">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="13d49-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="13d49-320">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="13d49-321">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="13d49-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="13d49-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="13d49-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="13d49-323">Java スクリプトオプション</span><span class="sxs-lookup"><span data-stu-id="13d49-323">JavaScript Option</span></span> | <span data-ttu-id="13d49-324">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-324">Default Value</span></span> | <span data-ttu-id="13d49-325">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="13d49-326">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="13d49-327">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-328">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-329">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="13d49-330">Java</span><span class="sxs-lookup"><span data-stu-id="13d49-330">Java</span></span>](#tab/java)

| <span data-ttu-id="13d49-331">Java オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-331">Java Option</span></span> | <span data-ttu-id="13d49-332">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-332">Default Value</span></span> | <span data-ttu-id="13d49-333">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="13d49-334">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="13d49-335">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-336">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-337">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="13d49-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="13d49-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="13d49-339">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-339">Empty</span></span> | <span data-ttu-id="13d49-340">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="13d49-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="13d49-341">NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="13d49-342">JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="13d49-343">Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="13d49-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="13d49-344">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="13d49-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="13d49-345">JSON/メッセージパックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="13d49-346">ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="13d49-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="13d49-347">各プロトコルには、シリアル化構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="13d49-348">JSON シリアル化は、メソッドの[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できる[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを`Startup.ConfigureServices`使用してサーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="13d49-349">この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="13d49-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="13d49-350">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、`JsonSerializerSettings`引数と戻り値のシリアル化を構成するために使用できるJSON.NETオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="13d49-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="13d49-351">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="13d49-352">たとえば、既定の "camelCase" の名前ではなく、"PascalCase" プロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="13d49-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="13d49-353">NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。</span><span class="sxs-lookup"><span data-stu-id="13d49-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="13d49-354">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="13d49-355">現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="13d49-356">メッセージ パックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-356">MessagePack serialization options</span></span>

<span data-ttu-id="13d49-357">メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="13d49-358">詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="13d49-359">現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="13d49-360">サーバー オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-360">Configure server options</span></span>

<span data-ttu-id="13d49-361">次の表に、SignalR ハブを設定するためのオプションを示します。</span><span class="sxs-lookup"><span data-stu-id="13d49-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="13d49-362">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-362">Option</span></span> | <span data-ttu-id="13d49-363">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-363">Default Value</span></span> | <span data-ttu-id="13d49-364">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="13d49-365">30 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-365">30 seconds</span></span> | <span data-ttu-id="13d49-366">この間隔でメッセージ (キープアライブを含む) を受信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="13d49-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="13d49-367">クライアントが実際に切断としてマークされるには、このタイムアウト間隔よりも長い時間がかかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="13d49-368">推奨値は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="13d49-369">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-369">15 seconds</span></span> | <span data-ttu-id="13d49-370">クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="13d49-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="13d49-371">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-372">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="13d49-373">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-373">15 seconds</span></span> | <span data-ttu-id="13d49-374">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="13d49-375">を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="13d49-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="13d49-376">推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="13d49-377">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="13d49-377">All installed protocols</span></span> | <span data-ttu-id="13d49-378">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="13d49-378">Protocols supported by this hub.</span></span> <span data-ttu-id="13d49-379">既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13d49-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="13d49-380">場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="13d49-381">これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="13d49-382">オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="13d49-383">単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="13d49-384">高度な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="13d49-385">トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="13d49-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="13d49-386">これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure`に渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="13d49-387">次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="13d49-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="13d49-388">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-388">Option</span></span> | <span data-ttu-id="13d49-389">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-389">Default Value</span></span> | <span data-ttu-id="13d49-390">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="13d49-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="13d49-391">32 KB</span></span> | <span data-ttu-id="13d49-392">サーバーがバッファリングするクライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="13d49-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="13d49-393">この値を大きくすると、サーバーはより大きなメッセージを受信できますが、メモリ消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="13d49-394">ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="13d49-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="13d49-395">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="13d49-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="13d49-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="13d49-396">32 KB</span></span> | <span data-ttu-id="13d49-397">サーバーがバッファーに格納するアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="13d49-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="13d49-398">この値を大きくすると、サーバーは大きなメッセージを送信できますが、メモリ消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="13d49-399">すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="13d49-399">All Transports are enabled.</span></span> | <span data-ttu-id="13d49-400">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。</span><span class="sxs-lookup"><span data-stu-id="13d49-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="13d49-401">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-401">See below.</span></span> | <span data-ttu-id="13d49-402">ロングポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="13d49-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="13d49-403">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-403">See below.</span></span> | <span data-ttu-id="13d49-404">WebSocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="13d49-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="13d49-405">ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="13d49-406">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-406">Option</span></span> | <span data-ttu-id="13d49-407">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-407">Default Value</span></span> | <span data-ttu-id="13d49-408">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="13d49-409">90秒</span><span class="sxs-lookup"><span data-stu-id="13d49-409">90 seconds</span></span> | <span data-ttu-id="13d49-410">1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="13d49-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="13d49-411">この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="13d49-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="13d49-412">WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="13d49-413">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-413">Option</span></span> | <span data-ttu-id="13d49-414">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-414">Default Value</span></span> | <span data-ttu-id="13d49-415">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="13d49-416">5 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-416">5 seconds</span></span> | <span data-ttu-id="13d49-417">サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="13d49-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="13d49-418">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="13d49-419">デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="13d49-420">クライアント オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-420">Configure client options</span></span>

<span data-ttu-id="13d49-421">クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。</span><span class="sxs-lookup"><span data-stu-id="13d49-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="13d49-422">Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。</span><span class="sxs-lookup"><span data-stu-id="13d49-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="13d49-423">ログの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-423">Configure logging</span></span>

<span data-ttu-id="13d49-424">ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="13d49-425">ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="13d49-426">詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="13d49-427">ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="13d49-428">詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="13d49-429">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。</span><span class="sxs-lookup"><span data-stu-id="13d49-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="13d49-430">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13d49-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="13d49-431">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="13d49-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="13d49-432">生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="13d49-433">ログは、ブラウザのコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="13d49-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="13d49-434">ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="13d49-435">ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="13d49-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="13d49-436">SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。</span><span class="sxs-lookup"><span data-stu-id="13d49-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="13d49-437">これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。</span><span class="sxs-lookup"><span data-stu-id="13d49-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="13d49-438">次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。</span><span class="sxs-lookup"><span data-stu-id="13d49-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="13d49-439">依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="13d49-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="13d49-440">これは無視しても問題ありません。</span><span class="sxs-lookup"><span data-stu-id="13d49-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="13d49-441">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-441">Configure allowed transports</span></span>

<span data-ttu-id="13d49-442">SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="13d49-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="13d49-443">の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="13d49-444">すべてのトランスポートは既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="13d49-444">All transports are enabled by default.</span></span>

<span data-ttu-id="13d49-445">たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="13d49-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="13d49-446">JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="13d49-447">このバージョンの Java クライアント Web ソケットは、使用可能なトランスポートのみです。</span><span class="sxs-lookup"><span data-stu-id="13d49-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="13d49-448">ベアラ認証の構成</span><span class="sxs-lookup"><span data-stu-id="13d49-448">Configure bearer authentication</span></span>

<span data-ttu-id="13d49-449">SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="13d49-450">NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。</span><span class="sxs-lookup"><span data-stu-id="13d49-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="13d49-451">JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="13d49-452">このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="13d49-453">NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="13d49-454">JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。</span><span class="sxs-lookup"><span data-stu-id="13d49-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="13d49-455">SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="13d49-456">を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。</span><span class="sxs-lookup"><span data-stu-id="13d49-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="13d49-457">[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="13d49-458">タイムアウトとキープアライブ オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13d49-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="13d49-459">`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="13d49-460">.NET</span><span class="sxs-lookup"><span data-stu-id="13d49-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="13d49-461">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-461">Option</span></span> | <span data-ttu-id="13d49-462">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-462">Default value</span></span> | <span data-ttu-id="13d49-463">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="13d49-464">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-465">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-465">Timeout for server activity.</span></span> <span data-ttu-id="13d49-466">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="13d49-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="13d49-467">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-468">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="13d49-469">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-469">15 seconds</span></span> | <span data-ttu-id="13d49-470">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="13d49-471">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="13d49-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="13d49-472">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-473">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="13d49-474">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-474">15 seconds</span></span> | <span data-ttu-id="13d49-475">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="13d49-476">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="13d49-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="13d49-477">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="13d49-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="13d49-478">NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="13d49-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="13d49-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="13d49-480">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-480">Option</span></span> | <span data-ttu-id="13d49-481">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-481">Default value</span></span> | <span data-ttu-id="13d49-482">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="13d49-483">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-484">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-484">Timeout for server activity.</span></span> <span data-ttu-id="13d49-485">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="13d49-486">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-487">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="13d49-488">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="13d49-489">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="13d49-490">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="13d49-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="13d49-491">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="13d49-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="13d49-492">Java</span><span class="sxs-lookup"><span data-stu-id="13d49-492">Java</span></span>](#tab/java)

| <span data-ttu-id="13d49-493">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-493">Option</span></span> | <span data-ttu-id="13d49-494">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-494">Default value</span></span> | <span data-ttu-id="13d49-495">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="13d49-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="13d49-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="13d49-497">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-498">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-498">Timeout for server activity.</span></span> <span data-ttu-id="13d49-499">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="13d49-500">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-501">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="13d49-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-502">15 seconds</span></span> | <span data-ttu-id="13d49-503">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="13d49-504">この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="13d49-505">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-506">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="13d49-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="13d49-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="13d49-508">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="13d49-509">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="13d49-510">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="13d49-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="13d49-511">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="13d49-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="13d49-512">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-512">Configure additional options</span></span>

<span data-ttu-id="13d49-513">追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="13d49-514">.NET</span><span class="sxs-lookup"><span data-stu-id="13d49-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="13d49-515">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-515">.NET Option</span></span> |  <span data-ttu-id="13d49-516">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-516">Default value</span></span> | <span data-ttu-id="13d49-517">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="13d49-518">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="13d49-519">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-520">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-521">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="13d49-522">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-522">Empty</span></span> | <span data-ttu-id="13d49-523">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="13d49-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="13d49-524">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-524">Empty</span></span> | <span data-ttu-id="13d49-525">すべての HTTP 要求で送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="13d49-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="13d49-526">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-526">Empty</span></span> | <span data-ttu-id="13d49-527">すべての HTTP 要求で送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="13d49-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="13d49-528">5 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-528">5 seconds</span></span> | <span data-ttu-id="13d49-529">ウェブソケットのみ。</span><span class="sxs-lookup"><span data-stu-id="13d49-529">WebSockets only.</span></span> <span data-ttu-id="13d49-530">サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="13d49-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="13d49-531">この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="13d49-532">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-532">Empty</span></span> | <span data-ttu-id="13d49-533">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="13d49-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="13d49-534">HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="13d49-535">Web ソケット接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="13d49-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="13d49-536">このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。</span><span class="sxs-lookup"><span data-stu-id="13d49-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="13d49-537">その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="13d49-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="13d49-538">**ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。**</span><span class="sxs-lookup"><span data-stu-id="13d49-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="13d49-539">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="13d49-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="13d49-540">HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="13d49-541">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="13d49-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="13d49-542">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="13d49-543">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="13d49-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="13d49-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="13d49-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="13d49-545">Java スクリプトオプション</span><span class="sxs-lookup"><span data-stu-id="13d49-545">JavaScript Option</span></span> | <span data-ttu-id="13d49-546">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-546">Default Value</span></span> | <span data-ttu-id="13d49-547">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="13d49-548">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="13d49-549">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-550">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-551">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="13d49-552">Java</span><span class="sxs-lookup"><span data-stu-id="13d49-552">Java</span></span>](#tab/java)

| <span data-ttu-id="13d49-553">Java オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-553">Java Option</span></span> | <span data-ttu-id="13d49-554">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-554">Default Value</span></span> | <span data-ttu-id="13d49-555">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="13d49-556">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="13d49-557">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-558">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-559">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="13d49-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="13d49-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="13d49-561">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-561">Empty</span></span> | <span data-ttu-id="13d49-562">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="13d49-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="13d49-563">NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="13d49-564">JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="13d49-565">Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="13d49-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="13d49-566">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="13d49-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="13d49-567">JSON/メッセージパックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="13d49-568">ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="13d49-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="13d49-569">各プロトコルには、シリアル化構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="13d49-570">JSON シリアル化は、メソッドの[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できる[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを`Startup.ConfigureServices`使用してサーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="13d49-571">この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="13d49-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="13d49-572">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、`JsonSerializerSettings`引数と戻り値のシリアル化を構成するために使用できるJSON.NETオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="13d49-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="13d49-573">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="13d49-574">たとえば、既定の "camelCase" の名前ではなく、"PascalCase" プロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="13d49-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="13d49-575">NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。</span><span class="sxs-lookup"><span data-stu-id="13d49-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="13d49-576">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="13d49-577">現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="13d49-578">メッセージ パックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-578">MessagePack serialization options</span></span>

<span data-ttu-id="13d49-579">メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="13d49-580">詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="13d49-581">現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="13d49-582">サーバー オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-582">Configure server options</span></span>

<span data-ttu-id="13d49-583">次の表に、SignalR ハブを設定するためのオプションを示します。</span><span class="sxs-lookup"><span data-stu-id="13d49-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="13d49-584">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-584">Option</span></span> | <span data-ttu-id="13d49-585">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-585">Default Value</span></span> | <span data-ttu-id="13d49-586">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="13d49-587">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-587">15 seconds</span></span> | <span data-ttu-id="13d49-588">クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="13d49-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="13d49-589">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-590">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="13d49-591">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-591">15 seconds</span></span> | <span data-ttu-id="13d49-592">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="13d49-593">を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="13d49-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="13d49-594">推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="13d49-595">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="13d49-595">All installed protocols</span></span> | <span data-ttu-id="13d49-596">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="13d49-596">Protocols supported by this hub.</span></span> <span data-ttu-id="13d49-597">既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13d49-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="13d49-598">場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="13d49-599">これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="13d49-600">オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="13d49-601">単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="13d49-602">高度な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="13d49-603">トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="13d49-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="13d49-604">これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure`に渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="13d49-605">次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="13d49-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="13d49-606">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-606">Option</span></span> | <span data-ttu-id="13d49-607">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-607">Default Value</span></span> | <span data-ttu-id="13d49-608">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="13d49-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="13d49-609">32 KB</span></span> | <span data-ttu-id="13d49-610">サーバーがバッファリングするクライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="13d49-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="13d49-611">この値を大きくすると、サーバーはより大きなメッセージを受信できますが、メモリ消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="13d49-612">ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="13d49-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="13d49-613">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="13d49-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="13d49-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="13d49-614">32 KB</span></span> | <span data-ttu-id="13d49-615">サーバーがバッファーに格納するアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="13d49-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="13d49-616">この値を大きくすると、サーバーは大きなメッセージを送信できますが、メモリ消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="13d49-617">すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="13d49-617">All Transports are enabled.</span></span> | <span data-ttu-id="13d49-618">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。</span><span class="sxs-lookup"><span data-stu-id="13d49-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="13d49-619">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-619">See below.</span></span> | <span data-ttu-id="13d49-620">ロングポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="13d49-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="13d49-621">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-621">See below.</span></span> | <span data-ttu-id="13d49-622">WebSocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="13d49-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="13d49-623">ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="13d49-624">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-624">Option</span></span> | <span data-ttu-id="13d49-625">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-625">Default Value</span></span> | <span data-ttu-id="13d49-626">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="13d49-627">90秒</span><span class="sxs-lookup"><span data-stu-id="13d49-627">90 seconds</span></span> | <span data-ttu-id="13d49-628">1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="13d49-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="13d49-629">この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="13d49-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="13d49-630">WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="13d49-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="13d49-631">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-631">Option</span></span> | <span data-ttu-id="13d49-632">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-632">Default Value</span></span> | <span data-ttu-id="13d49-633">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="13d49-634">5 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-634">5 seconds</span></span> | <span data-ttu-id="13d49-635">サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="13d49-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="13d49-636">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="13d49-637">デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="13d49-638">クライアント オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-638">Configure client options</span></span>

<span data-ttu-id="13d49-639">クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。</span><span class="sxs-lookup"><span data-stu-id="13d49-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="13d49-640">Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。</span><span class="sxs-lookup"><span data-stu-id="13d49-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="13d49-641">ログの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-641">Configure logging</span></span>

<span data-ttu-id="13d49-642">ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="13d49-643">ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="13d49-644">詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="13d49-645">ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="13d49-646">詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="13d49-647">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。</span><span class="sxs-lookup"><span data-stu-id="13d49-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="13d49-648">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="13d49-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="13d49-649">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="13d49-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="13d49-650">生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="13d49-651">ログは、ブラウザのコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="13d49-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="13d49-652">ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="13d49-653">ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="13d49-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="13d49-654">SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。</span><span class="sxs-lookup"><span data-stu-id="13d49-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="13d49-655">これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。</span><span class="sxs-lookup"><span data-stu-id="13d49-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="13d49-656">次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。</span><span class="sxs-lookup"><span data-stu-id="13d49-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="13d49-657">依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="13d49-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="13d49-658">これは無視しても問題ありません。</span><span class="sxs-lookup"><span data-stu-id="13d49-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="13d49-659">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-659">Configure allowed transports</span></span>

<span data-ttu-id="13d49-660">SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="13d49-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="13d49-661">の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="13d49-662">すべてのトランスポートは既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="13d49-662">All transports are enabled by default.</span></span>

<span data-ttu-id="13d49-663">たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="13d49-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="13d49-664">JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="13d49-665">ベアラ認証の構成</span><span class="sxs-lookup"><span data-stu-id="13d49-665">Configure bearer authentication</span></span>

<span data-ttu-id="13d49-666">SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="13d49-667">NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。</span><span class="sxs-lookup"><span data-stu-id="13d49-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="13d49-668">JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="13d49-669">このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="13d49-670">NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="13d49-671">JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。</span><span class="sxs-lookup"><span data-stu-id="13d49-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="13d49-672">SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="13d49-673">を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。</span><span class="sxs-lookup"><span data-stu-id="13d49-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="13d49-674">[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="13d49-675">タイムアウトとキープアライブ オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="13d49-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="13d49-676">`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="13d49-677">.NET</span><span class="sxs-lookup"><span data-stu-id="13d49-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="13d49-678">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-678">Option</span></span> | <span data-ttu-id="13d49-679">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-679">Default value</span></span> | <span data-ttu-id="13d49-680">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="13d49-681">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-682">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-682">Timeout for server activity.</span></span> <span data-ttu-id="13d49-683">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="13d49-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="13d49-684">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-685">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="13d49-686">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-686">15 seconds</span></span> | <span data-ttu-id="13d49-687">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="13d49-688">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="13d49-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="13d49-689">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-690">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="13d49-691">NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="13d49-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="13d49-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="13d49-693">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-693">Option</span></span> | <span data-ttu-id="13d49-694">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-694">Default value</span></span> | <span data-ttu-id="13d49-695">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="13d49-696">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-697">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-697">Timeout for server activity.</span></span> <span data-ttu-id="13d49-698">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="13d49-699">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-700">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="13d49-701">Java</span><span class="sxs-lookup"><span data-stu-id="13d49-701">Java</span></span>](#tab/java)

| <span data-ttu-id="13d49-702">オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-702">Option</span></span> | <span data-ttu-id="13d49-703">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-703">Default value</span></span> | <span data-ttu-id="13d49-704">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="13d49-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="13d49-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="13d49-706">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="13d49-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="13d49-707">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-707">Timeout for server activity.</span></span> <span data-ttu-id="13d49-708">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="13d49-709">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13d49-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="13d49-710">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="13d49-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="13d49-711">15 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-711">15 seconds</span></span> | <span data-ttu-id="13d49-712">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="13d49-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="13d49-713">この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="13d49-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="13d49-714">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="13d49-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="13d49-715">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13d49-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="13d49-716">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="13d49-716">Configure additional options</span></span>

<span data-ttu-id="13d49-717">追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="13d49-718">.NET</span><span class="sxs-lookup"><span data-stu-id="13d49-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="13d49-719">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-719">.NET Option</span></span> |  <span data-ttu-id="13d49-720">既定値</span><span class="sxs-lookup"><span data-stu-id="13d49-720">Default value</span></span> | <span data-ttu-id="13d49-721">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="13d49-722">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="13d49-723">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-724">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-725">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="13d49-726">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-726">Empty</span></span> | <span data-ttu-id="13d49-727">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="13d49-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="13d49-728">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-728">Empty</span></span> | <span data-ttu-id="13d49-729">すべての HTTP 要求で送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="13d49-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="13d49-730">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-730">Empty</span></span> | <span data-ttu-id="13d49-731">すべての HTTP 要求で送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="13d49-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="13d49-732">5 秒</span><span class="sxs-lookup"><span data-stu-id="13d49-732">5 seconds</span></span> | <span data-ttu-id="13d49-733">ウェブソケットのみ。</span><span class="sxs-lookup"><span data-stu-id="13d49-733">WebSockets only.</span></span> <span data-ttu-id="13d49-734">サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="13d49-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="13d49-735">この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="13d49-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="13d49-736">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-736">Empty</span></span> | <span data-ttu-id="13d49-737">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="13d49-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="13d49-738">HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="13d49-739">Web ソケット接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="13d49-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="13d49-740">このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。</span><span class="sxs-lookup"><span data-stu-id="13d49-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="13d49-741">その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="13d49-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="13d49-742">**ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。**</span><span class="sxs-lookup"><span data-stu-id="13d49-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="13d49-743">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="13d49-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="13d49-744">HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="13d49-745">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="13d49-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="13d49-746">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="13d49-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="13d49-747">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="13d49-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="13d49-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="13d49-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="13d49-749">Java スクリプトオプション</span><span class="sxs-lookup"><span data-stu-id="13d49-749">JavaScript Option</span></span> | <span data-ttu-id="13d49-750">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-750">Default Value</span></span> | <span data-ttu-id="13d49-751">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="13d49-752">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="13d49-753">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-754">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-755">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="13d49-756">Java</span><span class="sxs-lookup"><span data-stu-id="13d49-756">Java</span></span>](#tab/java)

| <span data-ttu-id="13d49-757">Java オプション</span><span class="sxs-lookup"><span data-stu-id="13d49-757">Java Option</span></span> | <span data-ttu-id="13d49-758">Default value</span><span class="sxs-lookup"><span data-stu-id="13d49-758">Default Value</span></span> | <span data-ttu-id="13d49-759">説明</span><span class="sxs-lookup"><span data-stu-id="13d49-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="13d49-760">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="13d49-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="13d49-761">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="13d49-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="13d49-762">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="13d49-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="13d49-763">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13d49-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="13d49-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="13d49-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="13d49-765">Empty</span><span class="sxs-lookup"><span data-stu-id="13d49-765">Empty</span></span> | <span data-ttu-id="13d49-766">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="13d49-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="13d49-767">NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="13d49-768">JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="13d49-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="13d49-769">Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="13d49-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="13d49-770">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="13d49-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
