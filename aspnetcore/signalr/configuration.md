---
title: ASP.NET Core SignalR構成
author: bradygaster
description: ASP.NET Core SignalRアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559003"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="16ed6-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="16ed6-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-105">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="16ed6-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="16ed6-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="16ed6-108">`AddJsonProtocol`は、 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に`Startup.ConfigureServices`追加できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="16ed6-109">メソッド`AddJsonProtocol`は、 `options`オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="16ed6-110">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、引数`System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions>と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="16ed6-111">詳細については、「system.string」[ドキュメント](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="16ed6-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを`Startup.ConfigureServices`構成するには、で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="16ed6-113">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に`AddJsonProtocol`同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="16ed6-114">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="16ed6-115">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="16ed6-116">Newtonsoft. Json に切り替える</span><span class="sxs-lookup"><span data-stu-id="16ed6-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="16ed6-117">で`System.Text.Json`サポートされて`Newtonsoft.Json`いないの機能が必要な場合は、「 [Newtonsoft. Json に切り替える」を](xref:migration/22-to-30#switch-to-newtonsoftjson)参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="16ed6-118">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-118">MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-119">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="16ed6-120">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-121">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="16ed6-122">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-122">Configure server options</span></span>

<span data-ttu-id="16ed6-123">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="16ed6-124">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-124">Option</span></span> | <span data-ttu-id="16ed6-125">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-125">Default Value</span></span> | <span data-ttu-id="16ed6-126">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="16ed6-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-127">30 seconds</span></span> | <span data-ttu-id="16ed6-128">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="16ed6-129">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="16ed6-130">推奨値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="16ed6-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-131">15 seconds</span></span> | <span data-ttu-id="16ed6-132">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="16ed6-133">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-134">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-135">15 seconds</span></span> | <span data-ttu-id="16ed6-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="16ed6-137">変更`KeepAliveInterval`する場合は、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="16ed6-138">`ServerTimeout` /推奨`serverTimeoutInMilliseconds`値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="16ed6-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="16ed6-139">All installed protocols</span></span> | <span data-ttu-id="16ed6-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="16ed6-140">Protocols supported by this hub.</span></span> <span data-ttu-id="16ed6-141">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="16ed6-142">の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="16ed6-143">既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="16ed6-144">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="16ed6-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="16ed6-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-146">32 KB</span></span> | <span data-ttu-id="16ed6-147">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="16ed6-148">オプションは、の`AddSignalR` `Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="16ed6-149">1つのハブのオプションは、で`AddSignalR`提供されるグローバルオプションを上書き<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>します。また、次のものを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="16ed6-150">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="16ed6-151">トランスポート`HttpConnectionDispatcherOptions`およびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="16ed6-152">これらのオプションは、で`Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="16ed6-153">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="16ed6-154">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-154">Option</span></span> | <span data-ttu-id="16ed6-155">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-155">Default Value</span></span> | <span data-ttu-id="16ed6-156">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="16ed6-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-157">32 KB</span></span> | <span data-ttu-id="16ed6-158">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="16ed6-159">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="16ed6-160">ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="16ed6-161">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="16ed6-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="16ed6-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-162">32 KB</span></span> | <span data-ttu-id="16ed6-163">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="16ed6-164">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="16ed6-165">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-165">All Transports are enabled.</span></span> | <span data-ttu-id="16ed6-166">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="16ed6-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="16ed6-167">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-167">See below.</span></span> | <span data-ttu-id="16ed6-168">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="16ed6-169">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-169">See below.</span></span> | <span data-ttu-id="16ed6-170">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="16ed6-171">0</span><span class="sxs-lookup"><span data-stu-id="16ed6-171">0</span></span> | <span data-ttu-id="16ed6-172">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="16ed6-173">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="16ed6-174">長いポーリングトランスポートには、次の`LongPolling`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="16ed6-175">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-175">Option</span></span> | <span data-ttu-id="16ed6-176">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-176">Default Value</span></span> | <span data-ttu-id="16ed6-177">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="16ed6-178">90秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-178">90 seconds</span></span> | <span data-ttu-id="16ed6-179">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="16ed6-180">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="16ed6-181">WebSocket トランスポートには、次の`WebSockets`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="16ed6-182">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-182">Option</span></span> | <span data-ttu-id="16ed6-183">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-183">Default Value</span></span> | <span data-ttu-id="16ed6-184">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="16ed6-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-185">5 seconds</span></span> | <span data-ttu-id="16ed6-186">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="16ed6-187">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="16ed6-188">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="16ed6-189">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-189">Configure client options</span></span>

<span data-ttu-id="16ed6-190">クライアントオプションは、 `HubConnectionBuilder` (.Net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="16ed6-191">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder`サブクラスは、ビルダーの構成オプションと`HubConnection`それ自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="16ed6-192">ログの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-192">Configure logging</span></span>

<span data-ttu-id="16ed6-193">ログは、 `ConfigureLogging` .net クライアントでメソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="16ed6-194">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="16ed6-195">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-196">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="16ed6-197">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="16ed6-198">たとえば、コンソールのログ記録を有効にする`Microsoft.Extensions.Logging.Console`には、NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="16ed6-199">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="16ed6-200">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="16ed6-201">生成する`LogLevel`ログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="16ed6-202">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="16ed6-203">`LogLevel`値の代わりに、ログレベル名を表す`string`値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="16ed6-204">これは、 `LogLevel`定数にアクセスできない環境で SignalR のログ記録を構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="16ed6-205">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-205">The following table lists the available log levels.</span></span> <span data-ttu-id="16ed6-206">ログに記録される`configureLogging` **最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="16ed6-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="16ed6-207">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="16ed6-208">String</span><span class="sxs-lookup"><span data-stu-id="16ed6-208">String</span></span>                      | <span data-ttu-id="16ed6-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="16ed6-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="16ed6-210">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="16ed6-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="16ed6-211">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="16ed6-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="16ed6-212">完全にログ記録を無効`signalR.LogLevel.None`にする`configureLogging`には、メソッドでを指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="16ed6-213">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="16ed6-214">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="16ed6-215">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="16ed6-216">次のコードスニペットは、SignalR Java `java.util.logging`クライアントでを使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="16ed6-217">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="16ed6-218">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="16ed6-219">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-219">Configure allowed transports</span></span>

<span data-ttu-id="16ed6-220">SignalR によって使用されるトランスポートは、 `WithUrl` (`withUrl` JavaScript では) の呼び出しで構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="16ed6-221">の`HttpTransportType`値のビットごとの or を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="16ed6-222">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-222">All transports are enabled by default.</span></span>

<span data-ttu-id="16ed6-223">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="16ed6-224">JavaScript クライアントでは、次のように指定さ`transport`れたオプションオブジェクトのフィールドを`withUrl`設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="16ed6-225">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="16ed6-226">Java クライアントでは、で`withTransport`メソッドを使用してトランスポートを選択`HttpHubConnectionBuilder`します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="16ed6-227">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="16ed6-228">SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="16ed6-229">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-229">Configure bearer authentication</span></span>

<span data-ttu-id="16ed6-230">SignalR 要求と共に認証データを提供するに`AccessTokenProvider`は、`accessTokenFactory` (JavaScript の) オプションを使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="16ed6-231">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization`の`Bearer`型のヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="16ed6-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="16ed6-232">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="16ed6-233">このような場合、アクセストークンはクエリ文字列値`access_token`として指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="16ed6-234">.NET クライアントでは、の`AccessTokenProvider` `WithUrl`オプションデリゲートを使用してオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="16ed6-235">JavaScript クライアントでは、の`accessTokenFactory` `withUrl`options オブジェクトのフィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="16ed6-236">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="16ed6-237">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[1\<つの文字列>](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="16ed6-238">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="16ed6-239">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="16ed6-240">タイムアウトとキープアライブの動作を構成するための追加の`HubConnection`オプションは、オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-241">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-242">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-242">Option</span></span> | <span data-ttu-id="16ed6-243">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-243">Default value</span></span> | <span data-ttu-id="16ed6-244">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="16ed6-245">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-246">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-246">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-247">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-248">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-249">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="16ed6-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-250">15 seconds</span></span> | <span data-ttu-id="16ed6-251">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-252">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-253">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-254">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-255">15 seconds</span></span> | <span data-ttu-id="16ed6-256">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-257">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-258">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="16ed6-259">.NET クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="16ed6-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-261">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-261">Option</span></span> | <span data-ttu-id="16ed6-262">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-262">Default value</span></span> | <span data-ttu-id="16ed6-263">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="16ed6-264">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-265">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-265">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-266">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="16ed6-267">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-268">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="16ed6-269">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-270">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-271">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-272">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-273">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-273">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-274">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-274">Option</span></span> | <span data-ttu-id="16ed6-275">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-275">Default value</span></span> | <span data-ttu-id="16ed6-276">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="16ed6-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="16ed6-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="16ed6-278">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-279">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-279">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-280">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-281">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-282">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="16ed6-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-283">15 seconds</span></span> | <span data-ttu-id="16ed6-284">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-285">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-286">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-287">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="16ed6-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="16ed6-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="16ed6-289">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-290">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-291">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-292">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="16ed6-293">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-293">Configure additional options</span></span>

<span data-ttu-id="16ed6-294">追加のオプションは、Java クライアント`WithUrl`の`withUrl`の`HttpHubConnectionBuilder` (JavaScript では`HubConnectionBuilder` ) メソッドまたはのさまざまな構成 api で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-295">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-296">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-296">.NET Option</span></span> |  <span data-ttu-id="16ed6-297">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-297">Default value</span></span> | <span data-ttu-id="16ed6-298">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="16ed6-299">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="16ed6-300">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-301">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-302">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="16ed6-303">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-303">Empty</span></span> | <span data-ttu-id="16ed6-304">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="16ed6-305">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-305">Empty</span></span> | <span data-ttu-id="16ed6-306">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="16ed6-307">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-307">Empty</span></span> | <span data-ttu-id="16ed6-308">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="16ed6-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="16ed6-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-309">5 seconds</span></span> | <span data-ttu-id="16ed6-310">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-310">WebSockets only.</span></span> <span data-ttu-id="16ed6-311">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="16ed6-312">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="16ed6-313">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-313">Empty</span></span> | <span data-ttu-id="16ed6-314">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="16ed6-315">HTTP 要求を送信するために`HttpMessageHandler`使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="16ed6-316">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="16ed6-317">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="16ed6-318">既定値の設定を変更して返すか、または新しい`HttpMessageHandler`インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="16ed6-319">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="16ed6-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="16ed6-320">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="16ed6-321">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="16ed6-322">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="16ed6-323">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="16ed6-324">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="16ed6-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-326">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-326">JavaScript Option</span></span> | <span data-ttu-id="16ed6-327">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-327">Default Value</span></span> | <span data-ttu-id="16ed6-328">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="16ed6-329">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="16ed6-330">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-331">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-332">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="16ed6-333">CORS 要求と共に資格情報を送信するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="16ed6-334">Azure App Service は固定セッションに cookie を使用します。このオプションを正しく動作させるには、このオプションを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="16ed6-335">SignalR を使用した CORS の詳細に<xref:signalr/security#cross-origin-resource-sharing>ついては、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-336">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-336">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-337">Java オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-337">Java Option</span></span> | <span data-ttu-id="16ed6-338">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-338">Default Value</span></span> | <span data-ttu-id="16ed6-339">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="16ed6-340">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="16ed6-341">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-342">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-343">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="16ed6-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="16ed6-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="16ed6-345">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-345">Empty</span></span> | <span data-ttu-id="16ed6-346">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="16ed6-347">.NET クライアントでは、これらのオプションは、に`WithUrl`用意されているオプションのデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="16ed6-348">JavaScript クライアントでは、次のように`withUrl`提供される javascript オブジェクトでこれらのオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="16ed6-349">Java クライアントでは、これらのオプションは、から返されるの`HttpHubConnectionBuilder`メソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="16ed6-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="16ed6-350">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="16ed6-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="16ed6-351">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-352">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="16ed6-353">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="16ed6-354">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="16ed6-355">`AddJsonProtocol`は、 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に`Startup.ConfigureServices`追加できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="16ed6-356">メソッド`AddJsonProtocol`は、 `options`オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="16ed6-357">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、引数`System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions>と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="16ed6-358">詳細については、「system.string」[ドキュメント](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="16ed6-359">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを`Startup.ConfigureServices`構成するには、で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="16ed6-360">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に`AddJsonProtocol`同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="16ed6-361">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="16ed6-362">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="16ed6-363">Newtonsoft. Json に切り替える</span><span class="sxs-lookup"><span data-stu-id="16ed6-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="16ed6-364">で`System.Text.Json`サポートされて`Newtonsoft.Json`いないの機能が必要な場合は、「 [Newtonsoft. Json に切り替える」を](xref:migration/22-to-30#switch-to-newtonsoftjson)参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="16ed6-365">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-365">MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-366">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="16ed6-367">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-368">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="16ed6-369">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-369">Configure server options</span></span>

<span data-ttu-id="16ed6-370">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="16ed6-371">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-371">Option</span></span> | <span data-ttu-id="16ed6-372">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-372">Default Value</span></span> | <span data-ttu-id="16ed6-373">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="16ed6-374">30 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-374">30 seconds</span></span> | <span data-ttu-id="16ed6-375">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="16ed6-376">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="16ed6-377">推奨値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="16ed6-378">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-378">15 seconds</span></span> | <span data-ttu-id="16ed6-379">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="16ed6-380">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-381">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-382">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-382">15 seconds</span></span> | <span data-ttu-id="16ed6-383">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="16ed6-384">変更`KeepAliveInterval`する場合は、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="16ed6-385">`ServerTimeout` /推奨`serverTimeoutInMilliseconds`値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="16ed6-386">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="16ed6-386">All installed protocols</span></span> | <span data-ttu-id="16ed6-387">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="16ed6-387">Protocols supported by this hub.</span></span> <span data-ttu-id="16ed6-388">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="16ed6-389">の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="16ed6-390">既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="16ed6-391">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="16ed6-392">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="16ed6-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-393">32 KB</span></span> | <span data-ttu-id="16ed6-394">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="16ed6-395">オプションは、の`AddSignalR` `Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="16ed6-396">1つのハブのオプションは、で`AddSignalR`提供されるグローバルオプションを上書き<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>します。また、次のものを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="16ed6-397">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="16ed6-398">トランスポート`HttpConnectionDispatcherOptions`およびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="16ed6-399">これらのオプションは、で`Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="16ed6-400">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="16ed6-401">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-401">Option</span></span> | <span data-ttu-id="16ed6-402">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-402">Default Value</span></span> | <span data-ttu-id="16ed6-403">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="16ed6-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-404">32 KB</span></span> | <span data-ttu-id="16ed6-405">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="16ed6-406">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="16ed6-407">ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="16ed6-408">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="16ed6-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="16ed6-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-409">32 KB</span></span> | <span data-ttu-id="16ed6-410">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="16ed6-411">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="16ed6-412">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-412">All Transports are enabled.</span></span> | <span data-ttu-id="16ed6-413">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="16ed6-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="16ed6-414">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-414">See below.</span></span> | <span data-ttu-id="16ed6-415">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="16ed6-416">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-416">See below.</span></span> | <span data-ttu-id="16ed6-417">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="16ed6-418">0</span><span class="sxs-lookup"><span data-stu-id="16ed6-418">0</span></span> | <span data-ttu-id="16ed6-419">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="16ed6-420">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="16ed6-421">長いポーリングトランスポートには、次の`LongPolling`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="16ed6-422">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-422">Option</span></span> | <span data-ttu-id="16ed6-423">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-423">Default Value</span></span> | <span data-ttu-id="16ed6-424">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="16ed6-425">90秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-425">90 seconds</span></span> | <span data-ttu-id="16ed6-426">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="16ed6-427">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="16ed6-428">WebSocket トランスポートには、次の`WebSockets`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="16ed6-429">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-429">Option</span></span> | <span data-ttu-id="16ed6-430">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-430">Default Value</span></span> | <span data-ttu-id="16ed6-431">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="16ed6-432">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-432">5 seconds</span></span> | <span data-ttu-id="16ed6-433">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="16ed6-434">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="16ed6-435">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="16ed6-436">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-436">Configure client options</span></span>

<span data-ttu-id="16ed6-437">クライアントオプションは、 `HubConnectionBuilder` (.Net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="16ed6-438">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder`サブクラスは、ビルダーの構成オプションと`HubConnection`それ自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="16ed6-439">ログの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-439">Configure logging</span></span>

<span data-ttu-id="16ed6-440">ログは、 `ConfigureLogging` .net クライアントでメソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="16ed6-441">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="16ed6-442">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-443">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="16ed6-444">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="16ed6-445">たとえば、コンソールのログ記録を有効にする`Microsoft.Extensions.Logging.Console`には、NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="16ed6-446">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="16ed6-447">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="16ed6-448">生成する`LogLevel`ログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="16ed6-449">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="16ed6-450">`LogLevel`値の代わりに、ログレベル名を表す`string`値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="16ed6-451">これは、 `LogLevel`定数にアクセスできない環境で SignalR のログ記録を構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="16ed6-452">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-452">The following table lists the available log levels.</span></span> <span data-ttu-id="16ed6-453">ログに記録される`configureLogging` **最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="16ed6-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="16ed6-454">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="16ed6-455">String</span><span class="sxs-lookup"><span data-stu-id="16ed6-455">String</span></span>                      | <span data-ttu-id="16ed6-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="16ed6-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="16ed6-457">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="16ed6-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="16ed6-458">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="16ed6-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="16ed6-459">完全にログ記録を無効`signalR.LogLevel.None`にする`configureLogging`には、メソッドでを指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="16ed6-460">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="16ed6-461">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="16ed6-462">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="16ed6-463">次のコードスニペットは、SignalR Java `java.util.logging`クライアントでを使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="16ed6-464">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="16ed6-465">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="16ed6-466">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-466">Configure allowed transports</span></span>

<span data-ttu-id="16ed6-467">SignalR によって使用されるトランスポートは、 `WithUrl` (`withUrl` JavaScript では) の呼び出しで構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="16ed6-468">の`HttpTransportType`値のビットごとの or を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="16ed6-469">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-469">All transports are enabled by default.</span></span>

<span data-ttu-id="16ed6-470">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="16ed6-471">JavaScript クライアントでは、次のように指定さ`transport`れたオプションオブジェクトのフィールドを`withUrl`設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="16ed6-472">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="16ed6-473">Java クライアントでは、で`withTransport`メソッドを使用してトランスポートを選択`HttpHubConnectionBuilder`します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="16ed6-474">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="16ed6-475">SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="16ed6-476">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-476">Configure bearer authentication</span></span>

<span data-ttu-id="16ed6-477">SignalR 要求と共に認証データを提供するに`AccessTokenProvider`は、`accessTokenFactory` (JavaScript の) オプションを使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="16ed6-478">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization`の`Bearer`型のヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="16ed6-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="16ed6-479">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="16ed6-480">このような場合、アクセストークンはクエリ文字列値`access_token`として指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="16ed6-481">.NET クライアントでは、の`AccessTokenProvider` `WithUrl`オプションデリゲートを使用してオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="16ed6-482">JavaScript クライアントでは、の`accessTokenFactory` `withUrl`options オブジェクトのフィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="16ed6-483">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="16ed6-484">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[1\<つの文字列>](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="16ed6-485">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="16ed6-486">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="16ed6-487">タイムアウトとキープアライブの動作を構成するための追加の`HubConnection`オプションは、オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-488">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-489">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-489">Option</span></span> | <span data-ttu-id="16ed6-490">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-490">Default value</span></span> | <span data-ttu-id="16ed6-491">[説明]</span><span class="sxs-lookup"><span data-stu-id="16ed6-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="16ed6-492">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-493">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-493">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-494">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-495">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-496">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="16ed6-497">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-497">15 seconds</span></span> | <span data-ttu-id="16ed6-498">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-499">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-500">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-501">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-502">15 seconds</span></span> | <span data-ttu-id="16ed6-503">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-504">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-505">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="16ed6-506">.NET クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="16ed6-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-508">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-508">Option</span></span> | <span data-ttu-id="16ed6-509">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-509">Default value</span></span> | <span data-ttu-id="16ed6-510">[説明]</span><span class="sxs-lookup"><span data-stu-id="16ed6-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="16ed6-511">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-512">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-512">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-513">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="16ed6-514">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-515">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="16ed6-516">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-517">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-518">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-519">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-520">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-520">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-521">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-521">Option</span></span> | <span data-ttu-id="16ed6-522">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-522">Default value</span></span> | <span data-ttu-id="16ed6-523">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="16ed6-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="16ed6-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="16ed6-525">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-526">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-526">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-527">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-528">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-529">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="16ed6-530">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-530">15 seconds</span></span> | <span data-ttu-id="16ed6-531">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-532">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-533">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-534">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="16ed6-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="16ed6-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="16ed6-536">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-537">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-538">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-539">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="16ed6-540">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-540">Configure additional options</span></span>

<span data-ttu-id="16ed6-541">追加のオプションは、Java クライアント`WithUrl`の`withUrl`の`HttpHubConnectionBuilder` (JavaScript では`HubConnectionBuilder` ) メソッドまたはのさまざまな構成 api で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-542">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-543">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-543">.NET Option</span></span> |  <span data-ttu-id="16ed6-544">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-544">Default value</span></span> | <span data-ttu-id="16ed6-545">[説明]</span><span class="sxs-lookup"><span data-stu-id="16ed6-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="16ed6-546">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="16ed6-547">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-548">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-549">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="16ed6-550">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-550">Empty</span></span> | <span data-ttu-id="16ed6-551">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="16ed6-552">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-552">Empty</span></span> | <span data-ttu-id="16ed6-553">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="16ed6-554">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-554">Empty</span></span> | <span data-ttu-id="16ed6-555">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="16ed6-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="16ed6-556">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-556">5 seconds</span></span> | <span data-ttu-id="16ed6-557">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-557">WebSockets only.</span></span> <span data-ttu-id="16ed6-558">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="16ed6-559">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="16ed6-560">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-560">Empty</span></span> | <span data-ttu-id="16ed6-561">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="16ed6-562">HTTP 要求を送信するために`HttpMessageHandler`使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="16ed6-563">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="16ed6-564">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="16ed6-565">既定値の設定を変更して返すか、または新しい`HttpMessageHandler`インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="16ed6-566">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="16ed6-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="16ed6-567">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="16ed6-568">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="16ed6-569">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="16ed6-570">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="16ed6-571">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="16ed6-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-573">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-573">JavaScript Option</span></span> | <span data-ttu-id="16ed6-574">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-574">Default Value</span></span> | <span data-ttu-id="16ed6-575">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="16ed6-576">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="16ed6-577">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-578">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-579">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-580">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-580">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-581">Java オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-581">Java Option</span></span> | <span data-ttu-id="16ed6-582">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-582">Default Value</span></span> | <span data-ttu-id="16ed6-583">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="16ed6-584">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="16ed6-585">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-586">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-587">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="16ed6-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="16ed6-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="16ed6-589">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-589">Empty</span></span> | <span data-ttu-id="16ed6-590">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="16ed6-591">.NET クライアントでは、これらのオプションは、に`WithUrl`用意されているオプションのデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="16ed6-592">JavaScript クライアントでは、次のように`withUrl`提供される javascript オブジェクトでこれらのオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="16ed6-593">Java クライアントでは、これらのオプションは、から返されるの`HttpHubConnectionBuilder`メソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="16ed6-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="16ed6-594">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="16ed6-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="16ed6-595">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-596">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="16ed6-597">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="16ed6-598">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="16ed6-599">`AddJsonProtocol`は、 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に`Startup.ConfigureServices`追加できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="16ed6-600">メソッド`AddJsonProtocol`は、 `options`オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="16ed6-601">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、引数`System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions>と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="16ed6-602">詳細については、「system.string」[ドキュメント](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="16ed6-603">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを`Startup.ConfigureServices`構成するには、で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="16ed6-604">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に`AddJsonProtocol`同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="16ed6-605">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="16ed6-606">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="16ed6-607">Newtonsoft. Json に切り替える</span><span class="sxs-lookup"><span data-stu-id="16ed6-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="16ed6-608">で`System.Text.Json`サポートされて`Newtonsoft.Json`いないの機能が必要な場合は、「 [Newtonsoft. Json に切り替える」を](xref:migration/22-to-30#switch-to-newtonsoftjson)参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="16ed6-609">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-609">MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-610">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="16ed6-611">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-612">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="16ed6-613">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-613">Configure server options</span></span>

<span data-ttu-id="16ed6-614">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="16ed6-615">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-615">Option</span></span> | <span data-ttu-id="16ed6-616">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-616">Default Value</span></span> | <span data-ttu-id="16ed6-617">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="16ed6-618">30 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-618">30 seconds</span></span> | <span data-ttu-id="16ed6-619">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="16ed6-620">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="16ed6-621">推奨値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="16ed6-622">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-622">15 seconds</span></span> | <span data-ttu-id="16ed6-623">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="16ed6-624">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-625">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-626">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-626">15 seconds</span></span> | <span data-ttu-id="16ed6-627">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="16ed6-628">変更`KeepAliveInterval`する場合は、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="16ed6-629">`ServerTimeout` /推奨`serverTimeoutInMilliseconds`値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="16ed6-630">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="16ed6-630">All installed protocols</span></span> | <span data-ttu-id="16ed6-631">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="16ed6-631">Protocols supported by this hub.</span></span> <span data-ttu-id="16ed6-632">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="16ed6-633">の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="16ed6-634">既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="16ed6-635">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="16ed6-636">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="16ed6-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-637">32 KB</span></span> | <span data-ttu-id="16ed6-638">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="16ed6-639">オプションは、の`AddSignalR` `Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="16ed6-640">1つのハブのオプションは、で`AddSignalR`提供されるグローバルオプションを上書き<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>します。また、次のものを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="16ed6-641">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="16ed6-642">トランスポート`HttpConnectionDispatcherOptions`およびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="16ed6-643">これらのオプションは、で`Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="16ed6-644">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="16ed6-645">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-645">Option</span></span> | <span data-ttu-id="16ed6-646">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-646">Default Value</span></span> | <span data-ttu-id="16ed6-647">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="16ed6-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-648">32 KB</span></span> | <span data-ttu-id="16ed6-649">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="16ed6-650">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="16ed6-651">ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="16ed6-652">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="16ed6-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="16ed6-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-653">32 KB</span></span> | <span data-ttu-id="16ed6-654">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="16ed6-655">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="16ed6-656">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-656">All Transports are enabled.</span></span> | <span data-ttu-id="16ed6-657">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="16ed6-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="16ed6-658">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-658">See below.</span></span> | <span data-ttu-id="16ed6-659">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="16ed6-660">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-660">See below.</span></span> | <span data-ttu-id="16ed6-661">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="16ed6-662">長いポーリングトランスポートには、次の`LongPolling`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="16ed6-663">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-663">Option</span></span> | <span data-ttu-id="16ed6-664">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-664">Default Value</span></span> | <span data-ttu-id="16ed6-665">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="16ed6-666">90秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-666">90 seconds</span></span> | <span data-ttu-id="16ed6-667">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="16ed6-668">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="16ed6-669">WebSocket トランスポートには、次の`WebSockets`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="16ed6-670">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-670">Option</span></span> | <span data-ttu-id="16ed6-671">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-671">Default Value</span></span> | <span data-ttu-id="16ed6-672">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="16ed6-673">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-673">5 seconds</span></span> | <span data-ttu-id="16ed6-674">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="16ed6-675">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="16ed6-676">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="16ed6-677">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-677">Configure client options</span></span>

<span data-ttu-id="16ed6-678">クライアントオプションは、 `HubConnectionBuilder` (.Net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="16ed6-679">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder`サブクラスは、ビルダーの構成オプションと`HubConnection`それ自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="16ed6-680">ログの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-680">Configure logging</span></span>

<span data-ttu-id="16ed6-681">ログは、 `ConfigureLogging` .net クライアントでメソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="16ed6-682">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="16ed6-683">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-684">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="16ed6-685">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="16ed6-686">たとえば、コンソールのログ記録を有効にする`Microsoft.Extensions.Logging.Console`には、NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="16ed6-687">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="16ed6-688">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="16ed6-689">生成する`LogLevel`ログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="16ed6-690">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="16ed6-691">`LogLevel`値の代わりに、ログレベル名を表す`string`値を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="16ed6-692">これは、 `LogLevel`定数にアクセスできない環境で SignalR のログ記録を構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="16ed6-693">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-693">The following table lists the available log levels.</span></span> <span data-ttu-id="16ed6-694">ログに記録される`configureLogging` **最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="16ed6-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="16ed6-695">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="16ed6-696">String</span><span class="sxs-lookup"><span data-stu-id="16ed6-696">String</span></span>                      | <span data-ttu-id="16ed6-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="16ed6-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="16ed6-698">`info` **or** `information`</span><span class="sxs-lookup"><span data-stu-id="16ed6-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="16ed6-699">`warn` **or** `warning`</span><span class="sxs-lookup"><span data-stu-id="16ed6-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="16ed6-700">完全にログ記録を無効`signalR.LogLevel.None`にする`configureLogging`には、メソッドでを指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="16ed6-701">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="16ed6-702">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="16ed6-703">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="16ed6-704">次のコードスニペットは、SignalR Java `java.util.logging`クライアントでを使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="16ed6-705">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="16ed6-706">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="16ed6-707">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-707">Configure allowed transports</span></span>

<span data-ttu-id="16ed6-708">SignalR によって使用されるトランスポートは、 `WithUrl` (`withUrl` JavaScript では) の呼び出しで構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="16ed6-709">の`HttpTransportType`値のビットごとの or を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="16ed6-710">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-710">All transports are enabled by default.</span></span>

<span data-ttu-id="16ed6-711">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="16ed6-712">JavaScript クライアントでは、次のように指定さ`transport`れたオプションオブジェクトのフィールドを`withUrl`設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="16ed6-713">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="16ed6-714">Java クライアントでは、で`withTransport`メソッドを使用してトランスポートを選択`HttpHubConnectionBuilder`します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="16ed6-715">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="16ed6-716">SignalR Java クライアントは、トランスポートフォールバックをまだサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="16ed6-717">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-717">Configure bearer authentication</span></span>

<span data-ttu-id="16ed6-718">SignalR 要求と共に認証データを提供するに`AccessTokenProvider`は、`accessTokenFactory` (JavaScript の) オプションを使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="16ed6-719">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization`の`Bearer`型のヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="16ed6-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="16ed6-720">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="16ed6-721">このような場合、アクセストークンはクエリ文字列値`access_token`として指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="16ed6-722">.NET クライアントでは、の`AccessTokenProvider` `WithUrl`オプションデリゲートを使用してオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="16ed6-723">JavaScript クライアントでは、の`accessTokenFactory` `withUrl`options オブジェクトのフィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="16ed6-724">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="16ed6-725">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[1\<つの文字列>](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="16ed6-726">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="16ed6-727">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="16ed6-728">タイムアウトとキープアライブの動作を構成するための追加の`HubConnection`オプションは、オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-729">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-730">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-730">Option</span></span> | <span data-ttu-id="16ed6-731">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-731">Default value</span></span> | <span data-ttu-id="16ed6-732">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="16ed6-733">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-734">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-734">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-735">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-736">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-737">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="16ed6-738">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-738">15 seconds</span></span> | <span data-ttu-id="16ed6-739">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-740">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-741">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-742">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-743">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-743">15 seconds</span></span> | <span data-ttu-id="16ed6-744">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-745">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-746">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="16ed6-747">.NET クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="16ed6-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-749">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-749">Option</span></span> | <span data-ttu-id="16ed6-750">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-750">Default value</span></span> | <span data-ttu-id="16ed6-751">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="16ed6-752">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-753">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-753">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-754">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="16ed6-755">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-756">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="16ed6-757">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-758">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-759">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-760">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-761">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-761">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-762">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-762">Option</span></span> | <span data-ttu-id="16ed6-763">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-763">Default value</span></span> | <span data-ttu-id="16ed6-764">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="16ed6-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="16ed6-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="16ed6-766">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-767">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-767">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-768">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-769">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-770">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="16ed6-771">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-771">15 seconds</span></span> | <span data-ttu-id="16ed6-772">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-773">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-774">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-775">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="16ed6-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="16ed6-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="16ed6-777">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-778">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-779">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-780">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="16ed6-781">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-781">Configure additional options</span></span>

<span data-ttu-id="16ed6-782">追加のオプションは、Java クライアント`WithUrl`の`withUrl`の`HttpHubConnectionBuilder` (JavaScript では`HubConnectionBuilder` ) メソッドまたはのさまざまな構成 api で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-783">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-784">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-784">.NET Option</span></span> |  <span data-ttu-id="16ed6-785">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-785">Default value</span></span> | <span data-ttu-id="16ed6-786">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="16ed6-787">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="16ed6-788">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-789">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-790">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="16ed6-791">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-791">Empty</span></span> | <span data-ttu-id="16ed6-792">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="16ed6-793">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-793">Empty</span></span> | <span data-ttu-id="16ed6-794">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="16ed6-795">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-795">Empty</span></span> | <span data-ttu-id="16ed6-796">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="16ed6-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="16ed6-797">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-797">5 seconds</span></span> | <span data-ttu-id="16ed6-798">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-798">WebSockets only.</span></span> <span data-ttu-id="16ed6-799">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="16ed6-800">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="16ed6-801">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-801">Empty</span></span> | <span data-ttu-id="16ed6-802">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="16ed6-803">HTTP 要求を送信するために`HttpMessageHandler`使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="16ed6-804">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="16ed6-805">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="16ed6-806">既定値の設定を変更して返すか、または新しい`HttpMessageHandler`インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="16ed6-807">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="16ed6-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="16ed6-808">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="16ed6-809">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="16ed6-810">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="16ed6-811">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="16ed6-812">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="16ed6-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-814">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-814">JavaScript Option</span></span> | <span data-ttu-id="16ed6-815">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-815">Default Value</span></span> | <span data-ttu-id="16ed6-816">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="16ed6-817">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="16ed6-818">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-819">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-820">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-821">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-821">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-822">Java オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-822">Java Option</span></span> | <span data-ttu-id="16ed6-823">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-823">Default Value</span></span> | <span data-ttu-id="16ed6-824">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="16ed6-825">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="16ed6-826">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-827">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-828">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="16ed6-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="16ed6-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="16ed6-830">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-830">Empty</span></span> | <span data-ttu-id="16ed6-831">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="16ed6-832">.NET クライアントでは、これらのオプションは、に`WithUrl`用意されているオプションのデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="16ed6-833">JavaScript クライアントでは、次のように`withUrl`提供される javascript オブジェクトでこれらのオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="16ed6-834">Java クライアントでは、これらのオプションは、から返されるの`HttpHubConnectionBuilder`メソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="16ed6-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="16ed6-835">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="16ed6-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="16ed6-836">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-837">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="16ed6-838">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="16ed6-839">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 。これは`Startup.ConfigureServices` 、メソッドの AddSignalR の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="16ed6-840">メソッド`AddJsonProtocol`は、 `options`オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="16ed6-841">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と`JsonSerializerSettings`戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="16ed6-842">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="16ed6-843">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するように`Startup.ConfigureServices`シリアライザーを構成するには、で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="16ed6-844">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に`AddJsonProtocol`同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="16ed6-845">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="16ed6-846">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="16ed6-847">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-847">MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-848">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="16ed6-849">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-850">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="16ed6-851">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-851">Configure server options</span></span>

<span data-ttu-id="16ed6-852">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="16ed6-853">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-853">Option</span></span> | <span data-ttu-id="16ed6-854">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-854">Default Value</span></span> | <span data-ttu-id="16ed6-855">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="16ed6-856">30 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-856">30 seconds</span></span> | <span data-ttu-id="16ed6-857">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="16ed6-858">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="16ed6-859">推奨値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="16ed6-860">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-860">15 seconds</span></span> | <span data-ttu-id="16ed6-861">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="16ed6-862">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-863">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-864">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-864">15 seconds</span></span> | <span data-ttu-id="16ed6-865">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="16ed6-866">変更`KeepAliveInterval`する場合は、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="16ed6-867">`ServerTimeout` /推奨`serverTimeoutInMilliseconds`値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="16ed6-868">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="16ed6-868">All installed protocols</span></span> | <span data-ttu-id="16ed6-869">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="16ed6-869">Protocols supported by this hub.</span></span> <span data-ttu-id="16ed6-870">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="16ed6-871">の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="16ed6-872">既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="16ed6-873">オプションは、の`AddSignalR` `Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="16ed6-874">1つのハブのオプションは、で`AddSignalR`提供されるグローバルオプションを上書き<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>します。また、次のものを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="16ed6-875">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="16ed6-876">トランスポート`HttpConnectionDispatcherOptions`およびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="16ed6-877">これらのオプションは、で`Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="16ed6-878">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="16ed6-879">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-879">Option</span></span> | <span data-ttu-id="16ed6-880">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-880">Default Value</span></span> | <span data-ttu-id="16ed6-881">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="16ed6-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-882">32 KB</span></span> | <span data-ttu-id="16ed6-883">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="16ed6-884">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="16ed6-885">ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="16ed6-886">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="16ed6-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="16ed6-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-887">32 KB</span></span> | <span data-ttu-id="16ed6-888">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="16ed6-889">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="16ed6-890">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-890">All Transports are enabled.</span></span> | <span data-ttu-id="16ed6-891">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="16ed6-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="16ed6-892">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-892">See below.</span></span> | <span data-ttu-id="16ed6-893">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="16ed6-894">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-894">See below.</span></span> | <span data-ttu-id="16ed6-895">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="16ed6-896">長いポーリングトランスポートには、次の`LongPolling`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="16ed6-897">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-897">Option</span></span> | <span data-ttu-id="16ed6-898">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-898">Default Value</span></span> | <span data-ttu-id="16ed6-899">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="16ed6-900">90秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-900">90 seconds</span></span> | <span data-ttu-id="16ed6-901">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="16ed6-902">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="16ed6-903">WebSocket トランスポートには、次の`WebSockets`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="16ed6-904">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-904">Option</span></span> | <span data-ttu-id="16ed6-905">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-905">Default Value</span></span> | <span data-ttu-id="16ed6-906">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="16ed6-907">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-907">5 seconds</span></span> | <span data-ttu-id="16ed6-908">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="16ed6-909">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="16ed6-910">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="16ed6-911">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-911">Configure client options</span></span>

<span data-ttu-id="16ed6-912">クライアントオプションは、 `HubConnectionBuilder` (.Net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="16ed6-913">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder`サブクラスは、ビルダーの構成オプションと`HubConnection`それ自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="16ed6-914">ログの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-914">Configure logging</span></span>

<span data-ttu-id="16ed6-915">ログは、 `ConfigureLogging` .net クライアントでメソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="16ed6-916">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="16ed6-917">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-918">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="16ed6-919">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="16ed6-920">たとえば、コンソールのログ記録を有効にする`Microsoft.Extensions.Logging.Console`には、NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="16ed6-921">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="16ed6-922">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="16ed6-923">生成する`LogLevel`ログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="16ed6-924">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="16ed6-925">完全にログ記録を無効`signalR.LogLevel.None`にする`configureLogging`には、メソッドでを指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="16ed6-926">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="16ed6-927">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="16ed6-928">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="16ed6-929">次のコードスニペットは、SignalR Java `java.util.logging`クライアントでを使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="16ed6-930">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="16ed6-931">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="16ed6-932">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-932">Configure allowed transports</span></span>

<span data-ttu-id="16ed6-933">SignalR によって使用されるトランスポートは、 `WithUrl` (`withUrl` JavaScript では) の呼び出しで構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="16ed6-934">の`HttpTransportType`値のビットごとの or を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="16ed6-935">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-935">All transports are enabled by default.</span></span>

<span data-ttu-id="16ed6-936">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="16ed6-937">JavaScript クライアントでは、次のように指定さ`transport`れたオプションオブジェクトのフィールドを`withUrl`設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="16ed6-938">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="16ed6-939">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-939">Configure bearer authentication</span></span>

<span data-ttu-id="16ed6-940">SignalR 要求と共に認証データを提供するに`AccessTokenProvider`は、`accessTokenFactory` (JavaScript の) オプションを使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="16ed6-941">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization`の`Bearer`型のヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="16ed6-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="16ed6-942">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="16ed6-943">このような場合、アクセストークンはクエリ文字列値`access_token`として指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="16ed6-944">.NET クライアントでは、の`AccessTokenProvider` `WithUrl`オプションデリゲートを使用してオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="16ed6-945">JavaScript クライアントでは、の`accessTokenFactory` `withUrl`options オブジェクトのフィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="16ed6-946">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="16ed6-947">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[1\<つの文字列>](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="16ed6-948">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="16ed6-949">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="16ed6-950">タイムアウトとキープアライブの動作を構成するための追加の`HubConnection`オプションは、オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-951">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-952">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-952">Option</span></span> | <span data-ttu-id="16ed6-953">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-953">Default value</span></span> | <span data-ttu-id="16ed6-954">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="16ed6-955">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-956">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-956">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-957">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-958">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-959">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="16ed6-960">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-960">15 seconds</span></span> | <span data-ttu-id="16ed6-961">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-962">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-963">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-964">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-965">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-965">15 seconds</span></span> | <span data-ttu-id="16ed6-966">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-967">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-968">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="16ed6-969">.NET クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="16ed6-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-971">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-971">Option</span></span> | <span data-ttu-id="16ed6-972">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-972">Default value</span></span> | <span data-ttu-id="16ed6-973">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="16ed6-974">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-975">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-975">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-976">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="16ed6-977">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-978">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="16ed6-979">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-980">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-981">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-982">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-983">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-983">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-984">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-984">Option</span></span> | <span data-ttu-id="16ed6-985">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-985">Default value</span></span> | <span data-ttu-id="16ed6-986">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="16ed6-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="16ed6-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="16ed6-988">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-989">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-989">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-990">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-991">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-992">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="16ed6-993">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-993">15 seconds</span></span> | <span data-ttu-id="16ed6-994">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-995">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-996">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-997">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="16ed6-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="16ed6-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="16ed6-999">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-1000">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="16ed6-1001">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="16ed6-1002">クライアントがサーバー上の`ClientTimeoutInterval`セットにメッセージを送信していない場合、サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="16ed6-1003">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-1003">Configure additional options</span></span>

<span data-ttu-id="16ed6-1004">追加のオプションは、Java クライアント`WithUrl`の`withUrl`の`HttpHubConnectionBuilder` (JavaScript では`HubConnectionBuilder` ) メソッドまたはのさまざまな構成 api で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-1006">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1006">.NET Option</span></span> |  <span data-ttu-id="16ed6-1007">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1007">Default value</span></span> | <span data-ttu-id="16ed6-1008">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="16ed6-1009">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="16ed6-1010">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-1011">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-1012">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="16ed6-1013">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1013">Empty</span></span> | <span data-ttu-id="16ed6-1014">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="16ed6-1015">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1015">Empty</span></span> | <span data-ttu-id="16ed6-1016">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="16ed6-1017">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1017">Empty</span></span> | <span data-ttu-id="16ed6-1018">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="16ed6-1019">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-1019">5 seconds</span></span> | <span data-ttu-id="16ed6-1020">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1020">WebSockets only.</span></span> <span data-ttu-id="16ed6-1021">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="16ed6-1022">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="16ed6-1023">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1023">Empty</span></span> | <span data-ttu-id="16ed6-1024">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="16ed6-1025">HTTP 要求を送信するために`HttpMessageHandler`使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="16ed6-1026">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="16ed6-1027">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="16ed6-1028">既定値の設定を変更して返すか、または新しい`HttpMessageHandler`インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="16ed6-1029">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="16ed6-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="16ed6-1030">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="16ed6-1031">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="16ed6-1032">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="16ed6-1033">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="16ed6-1034">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="16ed6-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-1036">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1036">JavaScript Option</span></span> | <span data-ttu-id="16ed6-1037">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-1037">Default Value</span></span> | <span data-ttu-id="16ed6-1038">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="16ed6-1039">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="16ed6-1040">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-1041">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-1042">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-1043">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-1044">Java オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1044">Java Option</span></span> | <span data-ttu-id="16ed6-1045">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-1045">Default Value</span></span> | <span data-ttu-id="16ed6-1046">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="16ed6-1047">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="16ed6-1048">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-1049">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-1050">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="16ed6-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="16ed6-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="16ed6-1052">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1052">Empty</span></span> | <span data-ttu-id="16ed6-1053">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="16ed6-1054">.NET クライアントでは、これらのオプションは、に`WithUrl`用意されているオプションのデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="16ed6-1055">JavaScript クライアントでは、次のように`withUrl`提供される javascript オブジェクトでこれらのオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="16ed6-1056">Java クライアントでは、これらのオプションは、から返されるの`HttpHubConnectionBuilder`メソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="16ed6-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="16ed6-1057">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="16ed6-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="16ed6-1058">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-1059">ASP.NET Core SignalR は、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="16ed6-1060">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="16ed6-1061">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) 。これは`Startup.ConfigureServices` 、メソッドの AddSignalR の後に追加できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="16ed6-1062">メソッド`AddJsonProtocol`は、 `options`オブジェクトを受け取るデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="16ed6-1063">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、引数と`JsonSerializerSettings`戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="16ed6-1064">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="16ed6-1065">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するように`Startup.ConfigureServices`シリアライザーを構成するには、で次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="16ed6-1066">.NET クライアントでは、 [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に`AddJsonProtocol`同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="16ed6-1067">拡張`Microsoft.Extensions.DependencyInjection`メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="16ed6-1068">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="16ed6-1069">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1069">MessagePack serialization options</span></span>

<span data-ttu-id="16ed6-1070">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="16ed6-1071">詳細については[、「SignalR の Messagepack](xref:signalr/messagepackhubprotocol) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-1072">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="16ed6-1073">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-1073">Configure server options</span></span>

<span data-ttu-id="16ed6-1074">次の表では、SignalR hub を構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="16ed6-1075">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1075">Option</span></span> | <span data-ttu-id="16ed6-1076">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1076">Default Value</span></span> | <span data-ttu-id="16ed6-1077">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="16ed6-1078">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-1078">15 seconds</span></span> | <span data-ttu-id="16ed6-1079">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="16ed6-1080">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-1081">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="16ed6-1082">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-1082">15 seconds</span></span> | <span data-ttu-id="16ed6-1083">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="16ed6-1084">変更`KeepAliveInterval`する場合は、 `ServerTimeout` / `serverTimeoutInMilliseconds`クライアントの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="16ed6-1085">`ServerTimeout` /推奨`serverTimeoutInMilliseconds`値は、値の`KeepAliveInterval`倍精度浮動小数点数です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="16ed6-1086">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="16ed6-1086">All installed protocols</span></span> | <span data-ttu-id="16ed6-1087">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="16ed6-1088">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="16ed6-1089">の`true`場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="16ed6-1090">既定値は`false`です。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="16ed6-1091">オプションは、の`AddSignalR` `Startup.ConfigureServices`呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="16ed6-1092">1つのハブのオプションは、で`AddSignalR`提供されるグローバルオプションを上書き<xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>します。また、次のものを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="16ed6-1093">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="16ed6-1094">トランスポート`HttpConnectionDispatcherOptions`およびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="16ed6-1095">これらのオプションは、で`Startup.Configure` [maphub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="16ed6-1096">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="16ed6-1097">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1097">Option</span></span> | <span data-ttu-id="16ed6-1098">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1098">Default Value</span></span> | <span data-ttu-id="16ed6-1099">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="16ed6-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-1100">32 KB</span></span> | <span data-ttu-id="16ed6-1101">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="16ed6-1102">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="16ed6-1103">ハブクラスに適用さ`Authorize`れた属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="16ed6-1104">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="16ed6-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="16ed6-1105">32 KB</span></span> | <span data-ttu-id="16ed6-1106">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="16ed6-1107">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="16ed6-1108">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1108">All Transports are enabled.</span></span> | <span data-ttu-id="16ed6-1109">クライアントが接続に使用`HttpTransportType`できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="16ed6-1110">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1110">See below.</span></span> | <span data-ttu-id="16ed6-1111">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="16ed6-1112">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1112">See below.</span></span> | <span data-ttu-id="16ed6-1113">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="16ed6-1114">長いポーリングトランスポートには、次の`LongPolling`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="16ed6-1115">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1115">Option</span></span> | <span data-ttu-id="16ed6-1116">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1116">Default Value</span></span> | <span data-ttu-id="16ed6-1117">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="16ed6-1118">90秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-1118">90 seconds</span></span> | <span data-ttu-id="16ed6-1119">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="16ed6-1120">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="16ed6-1121">WebSocket トランスポートには、次の`WebSockets`プロパティを使用して構成できる追加のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="16ed6-1122">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1122">Option</span></span> | <span data-ttu-id="16ed6-1123">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1123">Default Value</span></span> | <span data-ttu-id="16ed6-1124">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="16ed6-1125">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-1125">5 seconds</span></span> | <span data-ttu-id="16ed6-1126">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="16ed6-1127">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="16ed6-1128">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="16ed6-1129">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-1129">Configure client options</span></span>

<span data-ttu-id="16ed6-1130">クライアントオプションは、 `HubConnectionBuilder` (.Net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="16ed6-1131">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder`サブクラスは、ビルダーの構成オプションと`HubConnection`それ自体に含まれています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="16ed6-1132">ログの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-1132">Configure logging</span></span>

<span data-ttu-id="16ed6-1133">ログは、 `ConfigureLogging` .net クライアントでメソッドを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="16ed6-1134">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="16ed6-1135">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="16ed6-1136">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="16ed6-1137">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="16ed6-1138">たとえば、コンソールのログ記録を有効にする`Microsoft.Extensions.Logging.Console`には、NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="16ed6-1139">拡張メソッド`AddConsole`を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="16ed6-1140">JavaScript クライアントでは、同様`configureLogging`のメソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="16ed6-1141">生成する`LogLevel`ログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="16ed6-1142">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="16ed6-1143">完全にログ記録を無効`signalR.LogLevel.None`にする`configureLogging`には、メソッドでを指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="16ed6-1144">ログ記録の詳細については、 [SignalR Diagnostics のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="16ed6-1145">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="16ed6-1146">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="16ed6-1147">次のコードスニペットは、SignalR Java `java.util.logging`クライアントでを使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="16ed6-1148">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="16ed6-1149">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="16ed6-1150">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-1150">Configure allowed transports</span></span>

<span data-ttu-id="16ed6-1151">SignalR によって使用されるトランスポートは、 `WithUrl` (`withUrl` JavaScript では) の呼び出しで構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="16ed6-1152">の`HttpTransportType`値のビットごとの or を使用して、指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="16ed6-1153">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="16ed6-1154">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="16ed6-1155">JavaScript クライアントでは、次のように指定さ`transport`れたオプションオブジェクトのフィールドを`withUrl`設定することによって、トランスポートを構成します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="16ed6-1156">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-1156">Configure bearer authentication</span></span>

<span data-ttu-id="16ed6-1157">SignalR 要求と共に認証データを提供するに`AccessTokenProvider`は、`accessTokenFactory` (JavaScript の) オプションを使用して、目的のアクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="16ed6-1158">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization`の`Bearer`型のヘッダーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="16ed6-1159">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="16ed6-1160">このような場合、アクセストークンはクエリ文字列値`access_token`として指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="16ed6-1161">.NET クライアントでは、の`AccessTokenProvider` `WithUrl`オプションデリゲートを使用してオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="16ed6-1162">JavaScript クライアントでは、の`accessTokenFactory` `withUrl`options オブジェクトのフィールドを設定することにより、アクセストークンが構成されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="16ed6-1163">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="16ed6-1164">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[1\<つの文字列>](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="16ed6-1165">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="16ed6-1166">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="16ed6-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="16ed6-1167">タイムアウトとキープアライブの動作を構成するための追加の`HubConnection`オプションは、オブジェクト自体で利用できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-1169">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1169">Option</span></span> | <span data-ttu-id="16ed6-1170">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1170">Default value</span></span> | <span data-ttu-id="16ed6-1171">[説明]</span><span class="sxs-lookup"><span data-stu-id="16ed6-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="16ed6-1172">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-1173">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1173">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-1174">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断した`Closed`と見なし`onclose` 、(JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-1175">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-1176">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="16ed6-1177">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-1177">15 seconds</span></span> | <span data-ttu-id="16ed6-1178">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-1179">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (`onclose` JavaScript で) イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="16ed6-1180">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-1181">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="16ed6-1182">.NET クライアントでは、タイムアウト値は値と`TimeSpan`して指定されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="16ed6-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-1184">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1184">Option</span></span> | <span data-ttu-id="16ed6-1185">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1185">Default value</span></span> | <span data-ttu-id="16ed6-1186">[説明]</span><span class="sxs-lookup"><span data-stu-id="16ed6-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="16ed6-1187">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-1188">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1188">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-1189">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onclose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="16ed6-1190">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-1191">推奨値は、ping が到着するまでの時間を`KeepAliveInterval`考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-1192">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-1193">オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1193">Option</span></span> | <span data-ttu-id="16ed6-1194">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1194">Default value</span></span> | <span data-ttu-id="16ed6-1195">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="16ed6-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="16ed6-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="16ed6-1197">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="16ed6-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="16ed6-1198">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1198">Timeout for server activity.</span></span> <span data-ttu-id="16ed6-1199">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断し`onClose`たと見なし、イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-1200">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="16ed6-1201">推奨値は、サーバーの`KeepAliveInterval`値の少なくとも2倍の数で、ping が到着するまでの時間を確保します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="16ed6-1202">15 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-1202">15 seconds</span></span> | <span data-ttu-id="16ed6-1203">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="16ed6-1204">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `onClose`イベントをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="16ed6-1205">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="16ed6-1206">ハンドシェイクプロセスの詳細については、 [SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="16ed6-1207">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="16ed6-1207">Configure additional options</span></span>

<span data-ttu-id="16ed6-1208">追加のオプションは、Java クライアント`WithUrl`の`withUrl`の`HttpHubConnectionBuilder` (JavaScript では`HubConnectionBuilder` ) メソッドまたはのさまざまな構成 api で構成できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="16ed6-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="16ed6-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="16ed6-1210">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1210">.NET Option</span></span> |  <span data-ttu-id="16ed6-1211">既定値</span><span class="sxs-lookup"><span data-stu-id="16ed6-1211">Default value</span></span> | <span data-ttu-id="16ed6-1212">[説明]</span><span class="sxs-lookup"><span data-stu-id="16ed6-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="16ed6-1213">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="16ed6-1214">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-1215">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-1216">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="16ed6-1217">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1217">Empty</span></span> | <span data-ttu-id="16ed6-1218">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="16ed6-1219">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1219">Empty</span></span> | <span data-ttu-id="16ed6-1220">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="16ed6-1221">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1221">Empty</span></span> | <span data-ttu-id="16ed6-1222">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="16ed6-1223">5 秒</span><span class="sxs-lookup"><span data-stu-id="16ed6-1223">5 seconds</span></span> | <span data-ttu-id="16ed6-1224">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1224">WebSockets only.</span></span> <span data-ttu-id="16ed6-1225">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="16ed6-1226">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="16ed6-1227">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1227">Empty</span></span> | <span data-ttu-id="16ed6-1228">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="16ed6-1229">HTTP 要求を送信するために`HttpMessageHandler`使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="16ed6-1230">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="16ed6-1231">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="16ed6-1232">既定値の設定を変更して返すか、または新しい`HttpMessageHandler`インスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="16ed6-1233">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="16ed6-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="16ed6-1234">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="16ed6-1235">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="16ed6-1236">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="16ed6-1237">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="16ed6-1238">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="16ed6-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="16ed6-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="16ed6-1240">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1240">JavaScript Option</span></span> | <span data-ttu-id="16ed6-1241">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-1241">Default Value</span></span> | <span data-ttu-id="16ed6-1242">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="16ed6-1243">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="16ed6-1244">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-1245">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-1246">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="16ed6-1247">Java</span><span class="sxs-lookup"><span data-stu-id="16ed6-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="16ed6-1248">Java オプション</span><span class="sxs-lookup"><span data-stu-id="16ed6-1248">Java Option</span></span> | <span data-ttu-id="16ed6-1249">Default value</span><span class="sxs-lookup"><span data-stu-id="16ed6-1249">Default Value</span></span> | <span data-ttu-id="16ed6-1250">説明</span><span class="sxs-lookup"><span data-stu-id="16ed6-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="16ed6-1251">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="16ed6-1252">ネゴシエーションの手順`true`をスキップするには、これをに設定します。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="16ed6-1253">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="16ed6-1254">Azure SignalR サービスを使用している場合、この設定を有効にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="16ed6-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="16ed6-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="16ed6-1256">空</span><span class="sxs-lookup"><span data-stu-id="16ed6-1256">Empty</span></span> | <span data-ttu-id="16ed6-1257">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="16ed6-1258">.NET クライアントでは、これらのオプションは、に`WithUrl`用意されているオプションのデリゲートによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="16ed6-1259">JavaScript クライアントでは、次のように`withUrl`提供される javascript オブジェクトでこれらのオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="16ed6-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="16ed6-1260">Java クライアントでは、これらのオプションは、から返されるの`HttpHubConnectionBuilder`メソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="16ed6-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="16ed6-1261">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="16ed6-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
