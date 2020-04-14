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
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228141"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="394f1-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="394f1-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="394f1-104">JSON/メッセージパックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="394f1-105">ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="394f1-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="394f1-106">各プロトコルには、シリアル化構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="394f1-107">JSON シリアル化は[、AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="394f1-108">`AddJsonProtocol`の[後に追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)することができます`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="394f1-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="394f1-109">この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="394f1-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="394f1-110">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは`System.Text.Json`<xref:System.Text.Json.JsonSerializerOptions>、引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="394f1-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="394f1-111">詳細については[、System.Text.Json ドキュメントを参照してください](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="394f1-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="394f1-112">たとえば、既定の "camelCase" 名ではなく、プロパティ名の大文字と小文字を変更しないようにシリアライザーを構成するには、 で`Startup.ConfigureServices`次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="394f1-113">NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。</span><span class="sxs-lookup"><span data-stu-id="394f1-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="394f1-114">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="394f1-115">現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="394f1-116">ニュートンソフトに切り替える.</span><span class="sxs-lookup"><span data-stu-id="394f1-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="394f1-117">の機能`Newtonsoft.Json`が必要な場合は`System.Text.Json`、「 [Newtonsoft.Json に切り替える](xref:migration/22-to-30#switch-to-newtonsoftjson)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="394f1-118">メッセージ パックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-118">MessagePack serialization options</span></span>

<span data-ttu-id="394f1-119">メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="394f1-120">詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="394f1-121">現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="394f1-122">サーバー オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-122">Configure server options</span></span>

<span data-ttu-id="394f1-123">次の表に、SignalR ハブを設定するためのオプションを示します。</span><span class="sxs-lookup"><span data-stu-id="394f1-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="394f1-124">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-124">Option</span></span> | <span data-ttu-id="394f1-125">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-125">Default Value</span></span> | <span data-ttu-id="394f1-126">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="394f1-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-127">30 seconds</span></span> | <span data-ttu-id="394f1-128">この間隔でメッセージ (キープアライブを含む) を受信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="394f1-129">クライアントが実際に切断としてマークされるには、このタイムアウト間隔よりも長い時間がかかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="394f1-130">推奨値は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="394f1-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-131">15 seconds</span></span> | <span data-ttu-id="394f1-132">クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="394f1-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="394f1-133">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-134">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="394f1-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-135">15 seconds</span></span> | <span data-ttu-id="394f1-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="394f1-137">を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="394f1-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="394f1-138">推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="394f1-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="394f1-139">All installed protocols</span></span> | <span data-ttu-id="394f1-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="394f1-140">Protocols supported by this hub.</span></span> <span data-ttu-id="394f1-141">既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="394f1-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="394f1-142">場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="394f1-143">これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="394f1-144">クライアント アップロード ストリーム用にバッファリングできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="394f1-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="394f1-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="394f1-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-146">32 KB</span></span> | <span data-ttu-id="394f1-147">単一の受信ハブ メッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="394f1-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="394f1-148">オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="394f1-149">単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="394f1-150">高度な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="394f1-151">トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="394f1-152">これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure`に渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="394f1-153">次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="394f1-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="394f1-154">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-154">Option</span></span> | <span data-ttu-id="394f1-155">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-155">Default Value</span></span> | <span data-ttu-id="394f1-156">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="394f1-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-157">32 KB</span></span> | <span data-ttu-id="394f1-158">バックプレッシャを適用する前にサーバーがバッファリングするクライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="394f1-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="394f1-159">この値を大きくすると、バックプレッシャを適用せずに、サーバーがより大きなメッセージを受信する時間が増えますが、メモリ消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="394f1-160">ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="394f1-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="394f1-161">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="394f1-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="394f1-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-162">32 KB</span></span> | <span data-ttu-id="394f1-163">バックプレッシャを観察する前にサーバーがバッファーに入れるアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="394f1-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="394f1-164">この値を大きくすると、サーバーはバックプレッシャを待たずに大きなメッセージをより迅速にバッファリングできますが、メモリ消費量を増加させることができます。</span><span class="sxs-lookup"><span data-stu-id="394f1-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="394f1-165">すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="394f1-165">All Transports are enabled.</span></span> | <span data-ttu-id="394f1-166">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。</span><span class="sxs-lookup"><span data-stu-id="394f1-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="394f1-167">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-167">See below.</span></span> | <span data-ttu-id="394f1-168">ロングポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="394f1-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="394f1-169">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-169">See below.</span></span> | <span data-ttu-id="394f1-170">WebSocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="394f1-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="394f1-171">0</span><span class="sxs-lookup"><span data-stu-id="394f1-171">0</span></span> | <span data-ttu-id="394f1-172">ネゴシエート プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="394f1-173">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="394f1-174">ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="394f1-175">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-175">Option</span></span> | <span data-ttu-id="394f1-176">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-176">Default Value</span></span> | <span data-ttu-id="394f1-177">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="394f1-178">90秒</span><span class="sxs-lookup"><span data-stu-id="394f1-178">90 seconds</span></span> | <span data-ttu-id="394f1-179">1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="394f1-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="394f1-180">この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="394f1-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="394f1-181">WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="394f1-182">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-182">Option</span></span> | <span data-ttu-id="394f1-183">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-183">Default Value</span></span> | <span data-ttu-id="394f1-184">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="394f1-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-185">5 seconds</span></span> | <span data-ttu-id="394f1-186">サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="394f1-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="394f1-187">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="394f1-188">デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="394f1-189">クライアント オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-189">Configure client options</span></span>

<span data-ttu-id="394f1-190">クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。</span><span class="sxs-lookup"><span data-stu-id="394f1-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="394f1-191">Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。</span><span class="sxs-lookup"><span data-stu-id="394f1-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="394f1-192">ログの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-192">Configure logging</span></span>

<span data-ttu-id="394f1-193">ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="394f1-194">ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="394f1-195">詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="394f1-196">ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="394f1-197">詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="394f1-198">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。</span><span class="sxs-lookup"><span data-stu-id="394f1-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="394f1-199">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="394f1-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="394f1-200">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="394f1-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="394f1-201">生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="394f1-202">ログは、ブラウザのコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="394f1-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="394f1-203">値の`LogLevel`代わりに、ログ レベル名を`string`表す値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="394f1-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="394f1-204">これは、定数にアクセスできない環境で SignalR ロギングを設定する場合に`LogLevel`便利です。</span><span class="sxs-lookup"><span data-stu-id="394f1-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="394f1-205">次の表は、使用可能なログ レベルの一覧です。</span><span class="sxs-lookup"><span data-stu-id="394f1-205">The following table lists the available log levels.</span></span> <span data-ttu-id="394f1-206">指定する`configureLogging`値は、ログに記録される**最小**ログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="394f1-207">このレベルでログに記録されたメッセージ **、または表に示されたレベルの後に記録**されたメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="394f1-208">String</span><span class="sxs-lookup"><span data-stu-id="394f1-208">String</span></span>                      | <span data-ttu-id="394f1-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="394f1-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="394f1-210">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="394f1-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="394f1-211">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="394f1-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="394f1-212">ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="394f1-213">ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="394f1-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="394f1-214">SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。</span><span class="sxs-lookup"><span data-stu-id="394f1-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="394f1-215">これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。</span><span class="sxs-lookup"><span data-stu-id="394f1-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="394f1-216">次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。</span><span class="sxs-lookup"><span data-stu-id="394f1-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="394f1-217">依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="394f1-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="394f1-218">これは無視しても問題ありません。</span><span class="sxs-lookup"><span data-stu-id="394f1-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="394f1-219">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-219">Configure allowed transports</span></span>

<span data-ttu-id="394f1-220">SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="394f1-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="394f1-221">の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="394f1-222">すべてのトランスポートは既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="394f1-222">All transports are enabled by default.</span></span>

<span data-ttu-id="394f1-223">たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="394f1-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="394f1-224">JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="394f1-225">このバージョンの Java クライアント Web ソケットは、使用可能なトランスポートのみです。</span><span class="sxs-lookup"><span data-stu-id="394f1-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="394f1-226">Java クライアントでは、トランスポートは、 の`withTransport`メソッドで選択されます`HttpHubConnectionBuilder`。</span><span class="sxs-lookup"><span data-stu-id="394f1-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="394f1-227">Java クライアントは、デフォルトで WebSockets トランスポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="394f1-228">SignalR Java クライアントは、まだトランスポート フォールバックをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="394f1-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="394f1-229">ベアラ認証の構成</span><span class="sxs-lookup"><span data-stu-id="394f1-229">Configure bearer authentication</span></span>

<span data-ttu-id="394f1-230">SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="394f1-231">NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。</span><span class="sxs-lookup"><span data-stu-id="394f1-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="394f1-232">JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="394f1-233">このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="394f1-234">NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="394f1-235">JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。</span><span class="sxs-lookup"><span data-stu-id="394f1-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="394f1-236">SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="394f1-237">を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。</span><span class="sxs-lookup"><span data-stu-id="394f1-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="394f1-238">[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="394f1-239">タイムアウトとキープアライブ オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="394f1-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="394f1-240">`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="394f1-241">.NET</span><span class="sxs-lookup"><span data-stu-id="394f1-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="394f1-242">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-242">Option</span></span> | <span data-ttu-id="394f1-243">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-243">Default value</span></span> | <span data-ttu-id="394f1-244">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="394f1-245">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-246">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-246">Timeout for server activity.</span></span> <span data-ttu-id="394f1-247">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="394f1-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="394f1-248">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-249">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="394f1-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-250">15 seconds</span></span> | <span data-ttu-id="394f1-251">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="394f1-252">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="394f1-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="394f1-253">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-254">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="394f1-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-255">15 seconds</span></span> | <span data-ttu-id="394f1-256">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-257">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-258">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="394f1-259">NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="394f1-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="394f1-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="394f1-261">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-261">Option</span></span> | <span data-ttu-id="394f1-262">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-262">Default value</span></span> | <span data-ttu-id="394f1-263">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="394f1-264">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-265">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-265">Timeout for server activity.</span></span> <span data-ttu-id="394f1-266">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="394f1-267">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-268">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="394f1-269">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="394f1-270">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-271">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-272">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="394f1-273">Java</span><span class="sxs-lookup"><span data-stu-id="394f1-273">Java</span></span>](#tab/java)

| <span data-ttu-id="394f1-274">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-274">Option</span></span> | <span data-ttu-id="394f1-275">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-275">Default value</span></span> | <span data-ttu-id="394f1-276">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="394f1-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="394f1-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="394f1-278">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-279">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-279">Timeout for server activity.</span></span> <span data-ttu-id="394f1-280">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="394f1-281">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-282">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="394f1-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-283">15 seconds</span></span> | <span data-ttu-id="394f1-284">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="394f1-285">この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="394f1-286">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-287">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="394f1-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="394f1-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="394f1-289">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="394f1-290">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-291">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-292">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="394f1-293">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-293">Configure additional options</span></span>

<span data-ttu-id="394f1-294">追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="394f1-295">.NET</span><span class="sxs-lookup"><span data-stu-id="394f1-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="394f1-296">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-296">.NET Option</span></span> |  <span data-ttu-id="394f1-297">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-297">Default value</span></span> | <span data-ttu-id="394f1-298">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="394f1-299">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="394f1-300">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-301">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-302">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="394f1-303">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-303">Empty</span></span> | <span data-ttu-id="394f1-304">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="394f1-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="394f1-305">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-305">Empty</span></span> | <span data-ttu-id="394f1-306">すべての HTTP 要求で送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="394f1-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="394f1-307">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-307">Empty</span></span> | <span data-ttu-id="394f1-308">すべての HTTP 要求で送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="394f1-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="394f1-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-309">5 seconds</span></span> | <span data-ttu-id="394f1-310">ウェブソケットのみ。</span><span class="sxs-lookup"><span data-stu-id="394f1-310">WebSockets only.</span></span> <span data-ttu-id="394f1-311">サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="394f1-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="394f1-312">この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="394f1-313">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-313">Empty</span></span> | <span data-ttu-id="394f1-314">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="394f1-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="394f1-315">HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="394f1-316">Web ソケット接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="394f1-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="394f1-317">このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。</span><span class="sxs-lookup"><span data-stu-id="394f1-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="394f1-318">その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="394f1-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="394f1-319">**ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。**</span><span class="sxs-lookup"><span data-stu-id="394f1-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="394f1-320">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="394f1-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="394f1-321">HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="394f1-322">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="394f1-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="394f1-323">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="394f1-324">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="394f1-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="394f1-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="394f1-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="394f1-326">Java スクリプトオプション</span><span class="sxs-lookup"><span data-stu-id="394f1-326">JavaScript Option</span></span> | <span data-ttu-id="394f1-327">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-327">Default Value</span></span> | <span data-ttu-id="394f1-328">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="394f1-329">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="394f1-330">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-331">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-332">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="394f1-333">Java</span><span class="sxs-lookup"><span data-stu-id="394f1-333">Java</span></span>](#tab/java)

| <span data-ttu-id="394f1-334">Java オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-334">Java Option</span></span> | <span data-ttu-id="394f1-335">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-335">Default Value</span></span> | <span data-ttu-id="394f1-336">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="394f1-337">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="394f1-338">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-339">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-340">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="394f1-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="394f1-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="394f1-342">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-342">Empty</span></span> | <span data-ttu-id="394f1-343">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="394f1-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="394f1-344">NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="394f1-345">JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="394f1-346">Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="394f1-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="394f1-347">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="394f1-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="394f1-348">JSON/メッセージパックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="394f1-349">ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="394f1-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="394f1-350">各プロトコルには、シリアル化構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="394f1-351">JSON シリアル化は[、AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="394f1-352">`AddJsonProtocol`の[後に追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)することができます`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="394f1-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="394f1-353">この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="394f1-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="394f1-354">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは`System.Text.Json`<xref:System.Text.Json.JsonSerializerOptions>、引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="394f1-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="394f1-355">詳細については[、System.Text.Json ドキュメントを参照してください](/dotnet/api/system.text.json)。</span><span class="sxs-lookup"><span data-stu-id="394f1-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="394f1-356">たとえば、既定の "camelCase" 名ではなく、プロパティ名の大文字と小文字を変更しないようにシリアライザーを構成するには、 で`Startup.ConfigureServices`次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="394f1-357">NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。</span><span class="sxs-lookup"><span data-stu-id="394f1-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="394f1-358">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="394f1-359">現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="394f1-360">ニュートンソフトに切り替える.</span><span class="sxs-lookup"><span data-stu-id="394f1-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="394f1-361">の機能`Newtonsoft.Json`が必要な場合は`System.Text.Json`、「 [Newtonsoft.Json に切り替える](xref:migration/22-to-30#switch-to-newtonsoftjson)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="394f1-362">メッセージ パックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-362">MessagePack serialization options</span></span>

<span data-ttu-id="394f1-363">メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="394f1-364">詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="394f1-365">現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="394f1-366">サーバー オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-366">Configure server options</span></span>

<span data-ttu-id="394f1-367">次の表に、SignalR ハブを設定するためのオプションを示します。</span><span class="sxs-lookup"><span data-stu-id="394f1-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="394f1-368">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-368">Option</span></span> | <span data-ttu-id="394f1-369">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-369">Default Value</span></span> | <span data-ttu-id="394f1-370">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="394f1-371">30 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-371">30 seconds</span></span> | <span data-ttu-id="394f1-372">この間隔でメッセージ (キープアライブを含む) を受信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="394f1-373">クライアントが実際に切断としてマークされるには、このタイムアウト間隔よりも長い時間がかかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="394f1-374">推奨値は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="394f1-375">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-375">15 seconds</span></span> | <span data-ttu-id="394f1-376">クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="394f1-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="394f1-377">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-378">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="394f1-379">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-379">15 seconds</span></span> | <span data-ttu-id="394f1-380">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="394f1-381">を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="394f1-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="394f1-382">推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="394f1-383">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="394f1-383">All installed protocols</span></span> | <span data-ttu-id="394f1-384">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="394f1-384">Protocols supported by this hub.</span></span> <span data-ttu-id="394f1-385">既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="394f1-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="394f1-386">場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="394f1-387">これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="394f1-388">クライアント アップロード ストリーム用にバッファリングできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="394f1-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="394f1-389">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="394f1-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-390">32 KB</span></span> | <span data-ttu-id="394f1-391">単一の受信ハブ メッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="394f1-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="394f1-392">オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="394f1-393">単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="394f1-394">高度な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="394f1-395">トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="394f1-396">これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) `Startup.Configure`に渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="394f1-397">次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="394f1-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="394f1-398">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-398">Option</span></span> | <span data-ttu-id="394f1-399">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-399">Default Value</span></span> | <span data-ttu-id="394f1-400">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="394f1-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-401">32 KB</span></span> | <span data-ttu-id="394f1-402">バックプレッシャを適用する前にサーバーがバッファリングするクライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="394f1-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="394f1-403">この値を大きくすると、バックプレッシャを適用せずに、サーバーがより大きなメッセージを受信する時間が増えますが、メモリ消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="394f1-404">ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="394f1-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="394f1-405">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="394f1-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="394f1-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-406">32 KB</span></span> | <span data-ttu-id="394f1-407">バックプレッシャを観察する前にサーバーがバッファーに入れるアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="394f1-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="394f1-408">この値を大きくすると、サーバーはバックプレッシャを待たずに大きなメッセージをより迅速にバッファリングできますが、メモリ消費量を増加させることができます。</span><span class="sxs-lookup"><span data-stu-id="394f1-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="394f1-409">すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="394f1-409">All Transports are enabled.</span></span> | <span data-ttu-id="394f1-410">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。</span><span class="sxs-lookup"><span data-stu-id="394f1-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="394f1-411">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-411">See below.</span></span> | <span data-ttu-id="394f1-412">ロングポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="394f1-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="394f1-413">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-413">See below.</span></span> | <span data-ttu-id="394f1-414">WebSocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="394f1-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="394f1-415">ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="394f1-416">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-416">Option</span></span> | <span data-ttu-id="394f1-417">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-417">Default Value</span></span> | <span data-ttu-id="394f1-418">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="394f1-419">90秒</span><span class="sxs-lookup"><span data-stu-id="394f1-419">90 seconds</span></span> | <span data-ttu-id="394f1-420">1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="394f1-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="394f1-421">この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="394f1-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="394f1-422">WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="394f1-423">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-423">Option</span></span> | <span data-ttu-id="394f1-424">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-424">Default Value</span></span> | <span data-ttu-id="394f1-425">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="394f1-426">5 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-426">5 seconds</span></span> | <span data-ttu-id="394f1-427">サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="394f1-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="394f1-428">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="394f1-429">デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="394f1-430">クライアント オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-430">Configure client options</span></span>

<span data-ttu-id="394f1-431">クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。</span><span class="sxs-lookup"><span data-stu-id="394f1-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="394f1-432">Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。</span><span class="sxs-lookup"><span data-stu-id="394f1-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="394f1-433">ログの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-433">Configure logging</span></span>

<span data-ttu-id="394f1-434">ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="394f1-435">ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="394f1-436">詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="394f1-437">ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="394f1-438">詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="394f1-439">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。</span><span class="sxs-lookup"><span data-stu-id="394f1-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="394f1-440">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="394f1-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="394f1-441">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="394f1-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="394f1-442">生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="394f1-443">ログは、ブラウザのコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="394f1-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="394f1-444">値の`LogLevel`代わりに、ログ レベル名を`string`表す値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="394f1-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="394f1-445">これは、定数にアクセスできない環境で SignalR ロギングを設定する場合に`LogLevel`便利です。</span><span class="sxs-lookup"><span data-stu-id="394f1-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="394f1-446">次の表は、使用可能なログ レベルの一覧です。</span><span class="sxs-lookup"><span data-stu-id="394f1-446">The following table lists the available log levels.</span></span> <span data-ttu-id="394f1-447">指定する`configureLogging`値は、ログに記録される**最小**ログ レベルを設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="394f1-448">このレベルでログに記録されたメッセージ **、または表に示されたレベルの後に記録**されたメッセージがログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="394f1-449">String</span><span class="sxs-lookup"><span data-stu-id="394f1-449">String</span></span>                      | <span data-ttu-id="394f1-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="394f1-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="394f1-451">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="394f1-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="394f1-452">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="394f1-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="394f1-453">ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="394f1-454">ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="394f1-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="394f1-455">SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。</span><span class="sxs-lookup"><span data-stu-id="394f1-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="394f1-456">これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。</span><span class="sxs-lookup"><span data-stu-id="394f1-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="394f1-457">次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。</span><span class="sxs-lookup"><span data-stu-id="394f1-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="394f1-458">依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="394f1-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="394f1-459">これは無視しても問題ありません。</span><span class="sxs-lookup"><span data-stu-id="394f1-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="394f1-460">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-460">Configure allowed transports</span></span>

<span data-ttu-id="394f1-461">SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="394f1-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="394f1-462">の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="394f1-463">すべてのトランスポートは既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="394f1-463">All transports are enabled by default.</span></span>

<span data-ttu-id="394f1-464">たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="394f1-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="394f1-465">JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="394f1-466">このバージョンの Java クライアント Web ソケットは、使用可能なトランスポートのみです。</span><span class="sxs-lookup"><span data-stu-id="394f1-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="394f1-467">Java クライアントでは、トランスポートは、 の`withTransport`メソッドで選択されます`HttpHubConnectionBuilder`。</span><span class="sxs-lookup"><span data-stu-id="394f1-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="394f1-468">Java クライアントは、デフォルトで WebSockets トランスポートを使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="394f1-469">SignalR Java クライアントは、まだトランスポート フォールバックをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="394f1-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="394f1-470">ベアラ認証の構成</span><span class="sxs-lookup"><span data-stu-id="394f1-470">Configure bearer authentication</span></span>

<span data-ttu-id="394f1-471">SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="394f1-472">NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。</span><span class="sxs-lookup"><span data-stu-id="394f1-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="394f1-473">JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="394f1-474">このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="394f1-475">NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="394f1-476">JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。</span><span class="sxs-lookup"><span data-stu-id="394f1-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="394f1-477">SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="394f1-478">を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。</span><span class="sxs-lookup"><span data-stu-id="394f1-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="394f1-479">[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="394f1-480">タイムアウトとキープアライブ オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="394f1-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="394f1-481">`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="394f1-482">.NET</span><span class="sxs-lookup"><span data-stu-id="394f1-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="394f1-483">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-483">Option</span></span> | <span data-ttu-id="394f1-484">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-484">Default value</span></span> | <span data-ttu-id="394f1-485">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="394f1-486">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-487">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-487">Timeout for server activity.</span></span> <span data-ttu-id="394f1-488">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="394f1-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="394f1-489">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-490">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="394f1-491">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-491">15 seconds</span></span> | <span data-ttu-id="394f1-492">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="394f1-493">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="394f1-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="394f1-494">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-495">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="394f1-496">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-496">15 seconds</span></span> | <span data-ttu-id="394f1-497">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-498">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-499">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="394f1-500">NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="394f1-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="394f1-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="394f1-502">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-502">Option</span></span> | <span data-ttu-id="394f1-503">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-503">Default value</span></span> | <span data-ttu-id="394f1-504">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="394f1-505">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-506">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-506">Timeout for server activity.</span></span> <span data-ttu-id="394f1-507">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="394f1-508">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-509">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="394f1-510">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="394f1-511">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-512">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-513">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="394f1-514">Java</span><span class="sxs-lookup"><span data-stu-id="394f1-514">Java</span></span>](#tab/java)

| <span data-ttu-id="394f1-515">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-515">Option</span></span> | <span data-ttu-id="394f1-516">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-516">Default value</span></span> | <span data-ttu-id="394f1-517">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="394f1-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="394f1-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="394f1-519">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-520">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-520">Timeout for server activity.</span></span> <span data-ttu-id="394f1-521">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="394f1-522">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-523">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="394f1-524">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-524">15 seconds</span></span> | <span data-ttu-id="394f1-525">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="394f1-526">この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="394f1-527">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-528">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="394f1-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="394f1-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="394f1-530">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="394f1-531">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-532">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-533">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="394f1-534">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-534">Configure additional options</span></span>

<span data-ttu-id="394f1-535">追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="394f1-536">.NET</span><span class="sxs-lookup"><span data-stu-id="394f1-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="394f1-537">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-537">.NET Option</span></span> |  <span data-ttu-id="394f1-538">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-538">Default value</span></span> | <span data-ttu-id="394f1-539">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="394f1-540">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="394f1-541">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-542">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-543">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="394f1-544">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-544">Empty</span></span> | <span data-ttu-id="394f1-545">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="394f1-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="394f1-546">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-546">Empty</span></span> | <span data-ttu-id="394f1-547">すべての HTTP 要求で送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="394f1-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="394f1-548">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-548">Empty</span></span> | <span data-ttu-id="394f1-549">すべての HTTP 要求で送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="394f1-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="394f1-550">5 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-550">5 seconds</span></span> | <span data-ttu-id="394f1-551">ウェブソケットのみ。</span><span class="sxs-lookup"><span data-stu-id="394f1-551">WebSockets only.</span></span> <span data-ttu-id="394f1-552">サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="394f1-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="394f1-553">この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="394f1-554">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-554">Empty</span></span> | <span data-ttu-id="394f1-555">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="394f1-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="394f1-556">HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="394f1-557">Web ソケット接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="394f1-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="394f1-558">このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。</span><span class="sxs-lookup"><span data-stu-id="394f1-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="394f1-559">その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="394f1-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="394f1-560">**ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。**</span><span class="sxs-lookup"><span data-stu-id="394f1-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="394f1-561">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="394f1-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="394f1-562">HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="394f1-563">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="394f1-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="394f1-564">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="394f1-565">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="394f1-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="394f1-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="394f1-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="394f1-567">Java スクリプトオプション</span><span class="sxs-lookup"><span data-stu-id="394f1-567">JavaScript Option</span></span> | <span data-ttu-id="394f1-568">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-568">Default Value</span></span> | <span data-ttu-id="394f1-569">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="394f1-570">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="394f1-571">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-572">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-573">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="394f1-574">Java</span><span class="sxs-lookup"><span data-stu-id="394f1-574">Java</span></span>](#tab/java)

| <span data-ttu-id="394f1-575">Java オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-575">Java Option</span></span> | <span data-ttu-id="394f1-576">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-576">Default Value</span></span> | <span data-ttu-id="394f1-577">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="394f1-578">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="394f1-579">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-580">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-581">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="394f1-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="394f1-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="394f1-583">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-583">Empty</span></span> | <span data-ttu-id="394f1-584">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="394f1-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="394f1-585">NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="394f1-586">JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="394f1-587">Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="394f1-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="394f1-588">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="394f1-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="394f1-589">JSON/メッセージパックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="394f1-590">ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="394f1-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="394f1-591">各プロトコルには、シリアル化構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="394f1-592">JSON シリアル化は、メソッドの[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できる[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを`Startup.ConfigureServices`使用してサーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="394f1-593">この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="394f1-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="394f1-594">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、`JsonSerializerSettings`引数と戻り値のシリアル化を構成するために使用できるJSON.NETオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="394f1-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="394f1-595">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="394f1-596">たとえば、既定の "camelCase" の名前ではなく、"PascalCase" プロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="394f1-597">NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。</span><span class="sxs-lookup"><span data-stu-id="394f1-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="394f1-598">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="394f1-599">現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="394f1-600">メッセージ パックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-600">MessagePack serialization options</span></span>

<span data-ttu-id="394f1-601">メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="394f1-602">詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="394f1-603">現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="394f1-604">サーバー オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-604">Configure server options</span></span>

<span data-ttu-id="394f1-605">次の表に、SignalR ハブを設定するためのオプションを示します。</span><span class="sxs-lookup"><span data-stu-id="394f1-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="394f1-606">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-606">Option</span></span> | <span data-ttu-id="394f1-607">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-607">Default Value</span></span> | <span data-ttu-id="394f1-608">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="394f1-609">30 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-609">30 seconds</span></span> | <span data-ttu-id="394f1-610">この間隔でメッセージ (キープアライブを含む) を受信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="394f1-611">クライアントが実際に切断としてマークされるには、このタイムアウト間隔よりも長い時間がかかる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="394f1-612">推奨値は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="394f1-613">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-613">15 seconds</span></span> | <span data-ttu-id="394f1-614">クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="394f1-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="394f1-615">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-616">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="394f1-617">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-617">15 seconds</span></span> | <span data-ttu-id="394f1-618">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="394f1-619">を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="394f1-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="394f1-620">推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="394f1-621">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="394f1-621">All installed protocols</span></span> | <span data-ttu-id="394f1-622">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="394f1-622">Protocols supported by this hub.</span></span> <span data-ttu-id="394f1-623">既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="394f1-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="394f1-624">場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="394f1-625">これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="394f1-626">オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="394f1-627">単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="394f1-628">高度な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="394f1-629">トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="394f1-630">これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure`に渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="394f1-631">次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="394f1-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="394f1-632">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-632">Option</span></span> | <span data-ttu-id="394f1-633">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-633">Default Value</span></span> | <span data-ttu-id="394f1-634">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="394f1-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-635">32 KB</span></span> | <span data-ttu-id="394f1-636">サーバーがバッファリングするクライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="394f1-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="394f1-637">この値を大きくすると、サーバーはより大きなメッセージを受信できますが、メモリ消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="394f1-638">ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="394f1-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="394f1-639">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="394f1-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="394f1-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-640">32 KB</span></span> | <span data-ttu-id="394f1-641">サーバーがバッファーに格納するアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="394f1-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="394f1-642">この値を大きくすると、サーバーは大きなメッセージを送信できますが、メモリ消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="394f1-643">すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="394f1-643">All Transports are enabled.</span></span> | <span data-ttu-id="394f1-644">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。</span><span class="sxs-lookup"><span data-stu-id="394f1-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="394f1-645">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-645">See below.</span></span> | <span data-ttu-id="394f1-646">ロングポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="394f1-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="394f1-647">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-647">See below.</span></span> | <span data-ttu-id="394f1-648">WebSocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="394f1-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="394f1-649">ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="394f1-650">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-650">Option</span></span> | <span data-ttu-id="394f1-651">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-651">Default Value</span></span> | <span data-ttu-id="394f1-652">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="394f1-653">90秒</span><span class="sxs-lookup"><span data-stu-id="394f1-653">90 seconds</span></span> | <span data-ttu-id="394f1-654">1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="394f1-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="394f1-655">この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="394f1-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="394f1-656">WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="394f1-657">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-657">Option</span></span> | <span data-ttu-id="394f1-658">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-658">Default Value</span></span> | <span data-ttu-id="394f1-659">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="394f1-660">5 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-660">5 seconds</span></span> | <span data-ttu-id="394f1-661">サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="394f1-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="394f1-662">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="394f1-663">デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="394f1-664">クライアント オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-664">Configure client options</span></span>

<span data-ttu-id="394f1-665">クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。</span><span class="sxs-lookup"><span data-stu-id="394f1-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="394f1-666">Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。</span><span class="sxs-lookup"><span data-stu-id="394f1-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="394f1-667">ログの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-667">Configure logging</span></span>

<span data-ttu-id="394f1-668">ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="394f1-669">ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="394f1-670">詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="394f1-671">ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="394f1-672">詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="394f1-673">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。</span><span class="sxs-lookup"><span data-stu-id="394f1-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="394f1-674">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="394f1-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="394f1-675">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="394f1-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="394f1-676">生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="394f1-677">ログは、ブラウザのコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="394f1-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="394f1-678">ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="394f1-679">ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="394f1-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="394f1-680">SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。</span><span class="sxs-lookup"><span data-stu-id="394f1-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="394f1-681">これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。</span><span class="sxs-lookup"><span data-stu-id="394f1-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="394f1-682">次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。</span><span class="sxs-lookup"><span data-stu-id="394f1-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="394f1-683">依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="394f1-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="394f1-684">これは無視しても問題ありません。</span><span class="sxs-lookup"><span data-stu-id="394f1-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="394f1-685">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-685">Configure allowed transports</span></span>

<span data-ttu-id="394f1-686">SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="394f1-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="394f1-687">の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="394f1-688">すべてのトランスポートは既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="394f1-688">All transports are enabled by default.</span></span>

<span data-ttu-id="394f1-689">たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="394f1-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="394f1-690">JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="394f1-691">このバージョンの Java クライアント Web ソケットは、使用可能なトランスポートのみです。</span><span class="sxs-lookup"><span data-stu-id="394f1-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="394f1-692">ベアラ認証の構成</span><span class="sxs-lookup"><span data-stu-id="394f1-692">Configure bearer authentication</span></span>

<span data-ttu-id="394f1-693">SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="394f1-694">NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。</span><span class="sxs-lookup"><span data-stu-id="394f1-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="394f1-695">JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="394f1-696">このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="394f1-697">NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="394f1-698">JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。</span><span class="sxs-lookup"><span data-stu-id="394f1-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="394f1-699">SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="394f1-700">を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。</span><span class="sxs-lookup"><span data-stu-id="394f1-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="394f1-701">[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="394f1-702">タイムアウトとキープアライブ オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="394f1-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="394f1-703">`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="394f1-704">.NET</span><span class="sxs-lookup"><span data-stu-id="394f1-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="394f1-705">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-705">Option</span></span> | <span data-ttu-id="394f1-706">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-706">Default value</span></span> | <span data-ttu-id="394f1-707">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="394f1-708">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-709">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-709">Timeout for server activity.</span></span> <span data-ttu-id="394f1-710">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="394f1-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="394f1-711">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-712">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="394f1-713">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-713">15 seconds</span></span> | <span data-ttu-id="394f1-714">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="394f1-715">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="394f1-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="394f1-716">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-717">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="394f1-718">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-718">15 seconds</span></span> | <span data-ttu-id="394f1-719">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-720">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-721">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="394f1-722">NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="394f1-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="394f1-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="394f1-724">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-724">Option</span></span> | <span data-ttu-id="394f1-725">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-725">Default value</span></span> | <span data-ttu-id="394f1-726">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="394f1-727">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-728">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-728">Timeout for server activity.</span></span> <span data-ttu-id="394f1-729">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="394f1-730">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-731">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="394f1-732">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="394f1-733">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-734">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-735">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="394f1-736">Java</span><span class="sxs-lookup"><span data-stu-id="394f1-736">Java</span></span>](#tab/java)

| <span data-ttu-id="394f1-737">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-737">Option</span></span> | <span data-ttu-id="394f1-738">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-738">Default value</span></span> | <span data-ttu-id="394f1-739">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="394f1-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="394f1-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="394f1-741">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-742">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-742">Timeout for server activity.</span></span> <span data-ttu-id="394f1-743">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="394f1-744">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-745">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="394f1-746">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-746">15 seconds</span></span> | <span data-ttu-id="394f1-747">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="394f1-748">この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="394f1-749">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-750">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="394f1-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="394f1-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="394f1-752">15 秒 (15,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="394f1-753">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="394f1-754">クライアントからメッセージを送信すると、タイマーは間隔の開始にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="394f1-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="394f1-755">クライアントがサーバー上のセット内のメッセージを`ClientTimeoutInterval`送信していない場合、サーバーはクライアントが切断されたと見なします。</span><span class="sxs-lookup"><span data-stu-id="394f1-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="394f1-756">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-756">Configure additional options</span></span>

<span data-ttu-id="394f1-757">追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="394f1-758">.NET</span><span class="sxs-lookup"><span data-stu-id="394f1-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="394f1-759">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-759">.NET Option</span></span> |  <span data-ttu-id="394f1-760">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-760">Default value</span></span> | <span data-ttu-id="394f1-761">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="394f1-762">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="394f1-763">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-764">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-765">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="394f1-766">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-766">Empty</span></span> | <span data-ttu-id="394f1-767">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="394f1-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="394f1-768">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-768">Empty</span></span> | <span data-ttu-id="394f1-769">すべての HTTP 要求で送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="394f1-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="394f1-770">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-770">Empty</span></span> | <span data-ttu-id="394f1-771">すべての HTTP 要求で送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="394f1-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="394f1-772">5 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-772">5 seconds</span></span> | <span data-ttu-id="394f1-773">ウェブソケットのみ。</span><span class="sxs-lookup"><span data-stu-id="394f1-773">WebSockets only.</span></span> <span data-ttu-id="394f1-774">サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="394f1-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="394f1-775">この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="394f1-776">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-776">Empty</span></span> | <span data-ttu-id="394f1-777">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="394f1-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="394f1-778">HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="394f1-779">Web ソケット接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="394f1-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="394f1-780">このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。</span><span class="sxs-lookup"><span data-stu-id="394f1-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="394f1-781">その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="394f1-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="394f1-782">**ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。**</span><span class="sxs-lookup"><span data-stu-id="394f1-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="394f1-783">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="394f1-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="394f1-784">HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="394f1-785">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="394f1-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="394f1-786">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="394f1-787">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="394f1-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="394f1-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="394f1-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="394f1-789">Java スクリプトオプション</span><span class="sxs-lookup"><span data-stu-id="394f1-789">JavaScript Option</span></span> | <span data-ttu-id="394f1-790">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-790">Default Value</span></span> | <span data-ttu-id="394f1-791">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="394f1-792">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="394f1-793">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-794">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-795">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="394f1-796">Java</span><span class="sxs-lookup"><span data-stu-id="394f1-796">Java</span></span>](#tab/java)

| <span data-ttu-id="394f1-797">Java オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-797">Java Option</span></span> | <span data-ttu-id="394f1-798">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-798">Default Value</span></span> | <span data-ttu-id="394f1-799">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="394f1-800">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="394f1-801">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-802">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-803">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="394f1-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="394f1-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="394f1-805">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-805">Empty</span></span> | <span data-ttu-id="394f1-806">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="394f1-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="394f1-807">NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="394f1-808">JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="394f1-809">Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="394f1-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="394f1-810">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="394f1-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="394f1-811">JSON/メッセージパックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="394f1-812">ASP.NETコア SignalR は、メッセージをエンコードするための 2 つのプロトコルをサポートしています: [JSON](https://www.json.org/)と[MessagePack](https://msgpack.org/index.html)。</span><span class="sxs-lookup"><span data-stu-id="394f1-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="394f1-813">各プロトコルには、シリアル化構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="394f1-814">JSON シリアル化は、メソッドの[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加できる[AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを`Startup.ConfigureServices`使用してサーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="394f1-815">この`AddJsonProtocol`メソッドは、オブジェクトを受け取`options`るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="394f1-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="394f1-816">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、`JsonSerializerSettings`引数と戻り値のシリアル化を構成するために使用できるJSON.NETオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="394f1-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="394f1-817">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="394f1-818">たとえば、既定の "camelCase" の名前ではなく、"PascalCase" プロパティ名を使用するようにシリアライザーを構成するには、`Startup.ConfigureServices`で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="394f1-819">NET クライアントでは、同じ`AddJsonProtocol`拡張メソッドが[ハブ接続ビルダー](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に存在します。</span><span class="sxs-lookup"><span data-stu-id="394f1-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="394f1-820">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="394f1-821">現時点では、JavaScript クライアントで JSON シリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="394f1-822">メッセージ パックのシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-822">MessagePack serialization options</span></span>

<span data-ttu-id="394f1-823">メッセージ パックシリアル化は、[呼び出](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)しにデリゲートを提供することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="394f1-824">詳細については[、SignalR のメッセージ パック](xref:signalr/messagepackhubprotocol)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="394f1-825">現時点では、JavaScript クライアントでメッセージ パックのシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="394f1-826">サーバー オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-826">Configure server options</span></span>

<span data-ttu-id="394f1-827">次の表に、SignalR ハブを設定するためのオプションを示します。</span><span class="sxs-lookup"><span data-stu-id="394f1-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="394f1-828">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-828">Option</span></span> | <span data-ttu-id="394f1-829">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-829">Default Value</span></span> | <span data-ttu-id="394f1-830">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="394f1-831">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-831">15 seconds</span></span> | <span data-ttu-id="394f1-832">クライアントがこの時間内に最初のハンドシェイク メッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="394f1-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="394f1-833">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-834">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="394f1-835">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-835">15 seconds</span></span> | <span data-ttu-id="394f1-836">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにする ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="394f1-837">を変更`KeepAliveInterval`する場合は`ServerTimeout`/`serverTimeoutInMilliseconds`、クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="394f1-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="394f1-838">推奨値`ServerTimeout`/`serverTimeoutInMilliseconds`は 2`KeepAliveInterval`倍の値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="394f1-839">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="394f1-839">All installed protocols</span></span> | <span data-ttu-id="394f1-840">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="394f1-840">Protocols supported by this hub.</span></span> <span data-ttu-id="394f1-841">既定では、サーバーに登録されているすべてのプロトコルは許可されますが、この一覧からプロトコルを削除して、個々のハブの特定のプロトコルを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="394f1-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="394f1-842">場合`true`、ハブ メソッドで例外がスローされたときに、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="394f1-843">これらの例外メッセージ`false`には機密情報が含まれる可能性があるため、既定では 、 が使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="394f1-844">オプションは、 内の呼び出しに代えてオプションを`AddSignalR`提供することで`Startup.ConfigureServices`、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="394f1-845">単一のハブのオプションは、 で`AddSignalR`提供されるグローバル オプションを<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>オーバーライドし、 を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="394f1-846">高度な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="394f1-847">トランスポート`HttpConnectionDispatcherOptions`とメモリ バッファ管理に関連する詳細設定を構成するために使用します。</span><span class="sxs-lookup"><span data-stu-id="394f1-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="394f1-848">これらのオプションは、デリゲートを[MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) `Startup.Configure`に渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="394f1-849">次の表では、core SignalR の高度な HTTP オプションASP.NET設定するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="394f1-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="394f1-850">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-850">Option</span></span> | <span data-ttu-id="394f1-851">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-851">Default Value</span></span> | <span data-ttu-id="394f1-852">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="394f1-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-853">32 KB</span></span> | <span data-ttu-id="394f1-854">サーバーがバッファリングするクライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="394f1-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="394f1-855">この値を大きくすると、サーバーはより大きなメッセージを受信できますが、メモリ消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="394f1-856">ハブ クラスに`Authorize`適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="394f1-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="394f1-857">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="394f1-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="394f1-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="394f1-858">32 KB</span></span> | <span data-ttu-id="394f1-859">サーバーがバッファーに格納するアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="394f1-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="394f1-860">この値を大きくすると、サーバーは大きなメッセージを送信できますが、メモリ消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="394f1-861">すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="394f1-861">All Transports are enabled.</span></span> | <span data-ttu-id="394f1-862">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる値の列挙値をビット フラグにします。</span><span class="sxs-lookup"><span data-stu-id="394f1-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="394f1-863">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-863">See below.</span></span> | <span data-ttu-id="394f1-864">ロングポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="394f1-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="394f1-865">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-865">See below.</span></span> | <span data-ttu-id="394f1-866">WebSocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="394f1-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="394f1-867">ロング ポーリング トランスポートには、プロパティを使用して構成できる`LongPolling`追加オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="394f1-868">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-868">Option</span></span> | <span data-ttu-id="394f1-869">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-869">Default Value</span></span> | <span data-ttu-id="394f1-870">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="394f1-871">90秒</span><span class="sxs-lookup"><span data-stu-id="394f1-871">90 seconds</span></span> | <span data-ttu-id="394f1-872">1 回のポーリング要求を終了するまでに、サーバーがクライアントにメッセージを送信するまで待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="394f1-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="394f1-873">この値を小さくすると、クライアントは新しいポーリング要求を頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="394f1-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="394f1-874">WebSocket トランスポートには、プロパティを使用して構成できる追加`WebSockets`オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="394f1-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="394f1-875">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-875">Option</span></span> | <span data-ttu-id="394f1-876">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-876">Default Value</span></span> | <span data-ttu-id="394f1-877">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="394f1-878">5 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-878">5 seconds</span></span> | <span data-ttu-id="394f1-879">サーバーが閉じた後、この時間内にクライアントが閉じられなかった場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="394f1-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="394f1-880">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="394f1-881">デリゲートは、クライアントから要求された値を入力として受け取り、必要な値を返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="394f1-882">クライアント オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-882">Configure client options</span></span>

<span data-ttu-id="394f1-883">クライアント オプションは、型に`HubConnectionBuilder`対して構成できます (.NET クライアントおよび JavaScript クライアントで使用可能)。</span><span class="sxs-lookup"><span data-stu-id="394f1-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="394f1-884">Java クライアントでも使用できますが、`HttpHubConnectionBuilder`サブクラスにはビルダーの設定オプションとそれ自体が`HubConnection`含まれます。</span><span class="sxs-lookup"><span data-stu-id="394f1-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="394f1-885">ログの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-885">Configure logging</span></span>

<span data-ttu-id="394f1-886">ログ記録は、このメソッドを使用して`ConfigureLogging`.NET クライアントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="394f1-887">ログ プロバイダーとフィルターは、サーバー上と同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="394f1-888">詳細については[、ASP.NETコアのドキュメントのロギング](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="394f1-889">ログ プロバイダを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="394f1-890">詳細については、ドキュメントの[「組み込みログ プロバイダ](xref:fundamentals/logging/index#built-in-logging-providers)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="394f1-891">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールします`Microsoft.Extensions.Logging.Console`。</span><span class="sxs-lookup"><span data-stu-id="394f1-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="394f1-892">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="394f1-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="394f1-893">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="394f1-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="394f1-894">生成する`LogLevel`ログ メッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="394f1-895">ログは、ブラウザのコンソール ウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="394f1-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="394f1-896">ログの記録を完全に`signalR.LogLevel.None`無効にするには`configureLogging`、メソッドで指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="394f1-897">ログ記録の詳細については[、SignalR 診断のドキュメントを参照してください](xref:signalr/diagnostics)。</span><span class="sxs-lookup"><span data-stu-id="394f1-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="394f1-898">SignalR Java クライアントは[、SLF4J](https://www.slf4j.org/)ライブラリを使用してロギングします。</span><span class="sxs-lookup"><span data-stu-id="394f1-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="394f1-899">これは、ライブラリのユーザーが特定のロギング依存関係を持ち込むことによって、独自のログ記録実装を選択できるようにする高レベルのロギング API です。</span><span class="sxs-lookup"><span data-stu-id="394f1-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="394f1-900">次のコード スニペットは、SignalR Java クライアントでの使用方法`java.util.logging`を示しています。</span><span class="sxs-lookup"><span data-stu-id="394f1-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="394f1-901">依存関係でロギングを設定しない場合、SLF4J はデフォルトの操作なしロガーをロードし、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="394f1-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="394f1-902">これは無視しても問題ありません。</span><span class="sxs-lookup"><span data-stu-id="394f1-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="394f1-903">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-903">Configure allowed transports</span></span>

<span data-ttu-id="394f1-904">SignalR によって使用されるトランスポートは、呼び出`WithUrl`しで`withUrl`構成できます (JavaScript で)。</span><span class="sxs-lookup"><span data-stu-id="394f1-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="394f1-905">の値のビット単位の OR`HttpTransportType`を使用して、クライアントが指定されたトランスポートのみを使用するように制限できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="394f1-906">すべてのトランスポートは既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="394f1-906">All transports are enabled by default.</span></span>

<span data-ttu-id="394f1-907">たとえば、サーバー送信イベント トランスポートを無効にするが、WebSocket とロング ポーリング接続を許可するには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="394f1-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="394f1-908">JavaScript クライアントでは、トランスポートは、 に提供される`transport`オプション オブジェクトのフィールドを設定`withUrl`して構成されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="394f1-909">ベアラ認証の構成</span><span class="sxs-lookup"><span data-stu-id="394f1-909">Configure bearer authentication</span></span>

<span data-ttu-id="394f1-910">SignalR 要求と共に認証データを提供するには`AccessTokenProvider`、オプション`accessTokenFactory`(JavaScript で) を使用して、目的のアクセス トークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="394f1-911">NET クライアントでは、このアクセス トークンは HTTP "ベアラー認証" トークンとして渡されます`Authorization`(ヘッダーの種類を`Bearer`使用します)。</span><span class="sxs-lookup"><span data-stu-id="394f1-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="394f1-912">JavaScript クライアントでは、ブラウザー API がヘッダーの適用を制限する場合を除いて (特に、サーバー送信イベントと WebSocket 要求) 場合**を除き**、アクセス トークンはベアラー トークンとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="394f1-913">このような場合、アクセス トークンはクエリ文字列値`access_token`として提供されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="394f1-914">NET クライアントでは、 の`AccessTokenProvider`オプション デリゲートを使用してオプションを指定`WithUrl`できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="394f1-915">JavaScript クライアントでは、アクセス トークンは、 のオプション`accessTokenFactory`オブジェクトのフィールドを設定`withUrl`して構成します。</span><span class="sxs-lookup"><span data-stu-id="394f1-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="394f1-916">SignalR Java クライアントでは[、HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセス トークン ファクトリを提供することで、認証に使用するベアラー トークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="394f1-917">を使用[して](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)[、RxJava](https://github.com/ReactiveX/RxJava) [の単一\<文字列>](https://reactivex.io/documentation/single.html)を提供します。</span><span class="sxs-lookup"><span data-stu-id="394f1-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="394f1-918">[Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すと、クライアントのアクセス トークンを生成するロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="394f1-919">タイムアウトとキープアライブ オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="394f1-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="394f1-920">`HubConnection`タイムアウトとキープアライブ動作を設定するための追加オプションは、オブジェクト自体で使用できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="394f1-921">.NET</span><span class="sxs-lookup"><span data-stu-id="394f1-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="394f1-922">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-922">Option</span></span> | <span data-ttu-id="394f1-923">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-923">Default value</span></span> | <span data-ttu-id="394f1-924">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="394f1-925">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-926">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-926">Timeout for server activity.</span></span> <span data-ttu-id="394f1-927">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なし、イベントを`Closed`(JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="394f1-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="394f1-928">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-929">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="394f1-930">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-930">15 seconds</span></span> | <span data-ttu-id="394f1-931">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="394f1-932">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、`Closed`イベントを (JavaScript で)`onclose`トリガーします。</span><span class="sxs-lookup"><span data-stu-id="394f1-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="394f1-933">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-934">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="394f1-935">NET クライアントでは、タイムアウト値が値として`TimeSpan`指定されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="394f1-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="394f1-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="394f1-937">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-937">Option</span></span> | <span data-ttu-id="394f1-938">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-938">Default value</span></span> | <span data-ttu-id="394f1-939">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="394f1-940">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-941">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-941">Timeout for server activity.</span></span> <span data-ttu-id="394f1-942">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onclose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="394f1-943">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-944">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="394f1-945">Java</span><span class="sxs-lookup"><span data-stu-id="394f1-945">Java</span></span>](#tab/java)

| <span data-ttu-id="394f1-946">オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-946">Option</span></span> | <span data-ttu-id="394f1-947">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-947">Default value</span></span> | <span data-ttu-id="394f1-948">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="394f1-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="394f1-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="394f1-950">30 秒 (30,000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="394f1-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="394f1-951">サーバーアクティビティのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-951">Timeout for server activity.</span></span> <span data-ttu-id="394f1-952">この間隔でサーバーがメッセージを送信していない場合、クライアントはサーバーが切断されたと見なしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="394f1-953">この値は、サーバーから送信され **、** タイムアウト間隔内にクライアントが受信する ping メッセージに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="394f1-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="394f1-954">推奨値は、ping が到着するまでの時間を許容`KeepAliveInterval`するために、サーバーの値の少なくとも 2 倍の数値です。</span><span class="sxs-lookup"><span data-stu-id="394f1-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="394f1-955">15 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-955">15 seconds</span></span> | <span data-ttu-id="394f1-956">最初のサーバー ハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="394f1-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="394f1-957">この間隔でサーバーがハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルしてイベントをトリガー`onClose`します。</span><span class="sxs-lookup"><span data-stu-id="394f1-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="394f1-958">これは、ネットワーク遅延が深刻なためにハンドシェイク タイムアウト エラーが発生した場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="394f1-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="394f1-959">ハンドシェイク プロセスの詳細については[、SignalR ハブ プロトコル仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="394f1-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="394f1-960">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="394f1-960">Configure additional options</span></span>

<span data-ttu-id="394f1-961">追加オプション`WithUrl`は、Java`withUrl`クライアントの各種構成 API 上`HubConnectionBuilder`または JavaScript での`HttpHubConnectionBuilder`(JavaScript) メソッドで構成できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="394f1-962">.NET</span><span class="sxs-lookup"><span data-stu-id="394f1-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="394f1-963">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-963">.NET Option</span></span> |  <span data-ttu-id="394f1-964">既定値</span><span class="sxs-lookup"><span data-stu-id="394f1-964">Default value</span></span> | <span data-ttu-id="394f1-965">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="394f1-966">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="394f1-967">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-968">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-969">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="394f1-970">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-970">Empty</span></span> | <span data-ttu-id="394f1-971">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="394f1-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="394f1-972">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-972">Empty</span></span> | <span data-ttu-id="394f1-973">すべての HTTP 要求で送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="394f1-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="394f1-974">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-974">Empty</span></span> | <span data-ttu-id="394f1-975">すべての HTTP 要求で送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="394f1-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="394f1-976">5 秒</span><span class="sxs-lookup"><span data-stu-id="394f1-976">5 seconds</span></span> | <span data-ttu-id="394f1-977">ウェブソケットのみ。</span><span class="sxs-lookup"><span data-stu-id="394f1-977">WebSockets only.</span></span> <span data-ttu-id="394f1-978">サーバーがクローズ要求を確認するまでの、クライアントがクローズ後に待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="394f1-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="394f1-979">この時間内にサーバーがクローズを確認しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="394f1-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="394f1-980">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-980">Empty</span></span> | <span data-ttu-id="394f1-981">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="394f1-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="394f1-982">HTTP 要求の送信に使用されるを`HttpMessageHandler`構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="394f1-983">Web ソケット接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="394f1-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="394f1-984">このデリゲートは null 以外の値を返す必要があり、既定値をパラメーターとして受け取ります。</span><span class="sxs-lookup"><span data-stu-id="394f1-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="394f1-985">その既定値の設定を変更して返すか、新`HttpMessageHandler`しいインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="394f1-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="394f1-986">**ハンドラを置き換える場合は、指定されたハンドラから保持する設定を必ずコピーし、そうでない場合は、設定されたオプション (Cookie や Headers など) が新しいハンドラに適用されないようにしてください。**</span><span class="sxs-lookup"><span data-stu-id="394f1-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="394f1-987">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="394f1-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="394f1-988">HTTP および WebSocket 要求の既定の資格情報を送信するには、このブール値を設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="394f1-989">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="394f1-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="394f1-990">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="394f1-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="394f1-991">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="394f1-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="394f1-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="394f1-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="394f1-993">Java スクリプトオプション</span><span class="sxs-lookup"><span data-stu-id="394f1-993">JavaScript Option</span></span> | <span data-ttu-id="394f1-994">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-994">Default Value</span></span> | <span data-ttu-id="394f1-995">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="394f1-996">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="394f1-997">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-998">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-999">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="394f1-1000">Java</span><span class="sxs-lookup"><span data-stu-id="394f1-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="394f1-1001">Java オプション</span><span class="sxs-lookup"><span data-stu-id="394f1-1001">Java Option</span></span> | <span data-ttu-id="394f1-1002">Default value</span><span class="sxs-lookup"><span data-stu-id="394f1-1002">Default Value</span></span> | <span data-ttu-id="394f1-1003">説明</span><span class="sxs-lookup"><span data-stu-id="394f1-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="394f1-1004">HTTP 要求でベアラー認証トークンとして提供される文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="394f1-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="394f1-1005">ネゴシエーションステップを`true`スキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="394f1-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="394f1-1006">**WebSockets トランスポートが唯一有効なトランスポートである場合にのみサポートされます**。</span><span class="sxs-lookup"><span data-stu-id="394f1-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="394f1-1007">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="394f1-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="394f1-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="394f1-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="394f1-1009">Empty</span><span class="sxs-lookup"><span data-stu-id="394f1-1009">Empty</span></span> | <span data-ttu-id="394f1-1010">すべての HTTP 要求で送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="394f1-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="394f1-1011">NET クライアントでは、これらのオプションは、 に提供されるオプション デリゲートによって`WithUrl`変更できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="394f1-1012">JavaScript クライアントでは、これらのオプションは`withUrl`、次の JavaScript オブジェクトで提供できます。</span><span class="sxs-lookup"><span data-stu-id="394f1-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="394f1-1013">Java クライアントでは、これらのオプションは、そこから返されるメソッドで構成`HttpHubConnectionBuilder`できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="394f1-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="394f1-1014">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="394f1-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
