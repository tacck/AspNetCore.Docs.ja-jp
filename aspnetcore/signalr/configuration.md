---
title: ASP.NET Core SignalR の構成
author: bradygaster
description: ASP.NET Core アプリを構成する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
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
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393874"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="eefc6-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eefc6-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-105">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eefc6-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eefc6-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="eefc6-108">`AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eefc6-109">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eefc6-110">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eefc6-111">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="eefc6-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="eefc6-113">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eefc6-114">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eefc6-115">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="eefc6-116">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="eefc6-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="eefc6-117">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eefc6-118">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-118">MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-119">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eefc6-120">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-121">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eefc6-122">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-122">Configure server options</span></span>

<span data-ttu-id="eefc6-123">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eefc6-124">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-124">Option</span></span> | <span data-ttu-id="eefc6-125">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-125">Default Value</span></span> | <span data-ttu-id="eefc6-126">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="eefc6-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-127">30 seconds</span></span> | <span data-ttu-id="eefc6-128">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="eefc6-129">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="eefc6-130">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="eefc6-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-131">15 seconds</span></span> | <span data-ttu-id="eefc6-132">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eefc6-133">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-134">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-135">15 seconds</span></span> | <span data-ttu-id="eefc6-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eefc6-137">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eefc6-138">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eefc6-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="eefc6-139">All installed protocols</span></span> | <span data-ttu-id="eefc6-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="eefc6-140">Protocols supported by this hub.</span></span> <span data-ttu-id="eefc6-141">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eefc6-142">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eefc6-143">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="eefc6-144">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="eefc6-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="eefc6-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-146">32 KB</span></span> | <span data-ttu-id="eefc6-147">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="eefc6-148">1</span><span class="sxs-lookup"><span data-stu-id="eefc6-148">1</span></span> | <span data-ttu-id="eefc6-149">各クライアントがキューに入れる前に並列で呼び出すことができるハブメソッドの最大数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="eefc6-150">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eefc6-151">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eefc6-152">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eefc6-153">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eefc6-154">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eefc6-155">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eefc6-156">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-156">Option</span></span> | <span data-ttu-id="eefc6-157">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-157">Default Value</span></span> | <span data-ttu-id="eefc6-158">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eefc6-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-159">32 KB</span></span> | <span data-ttu-id="eefc6-160">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="eefc6-161">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eefc6-162">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eefc6-163">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="eefc6-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eefc6-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-164">32 KB</span></span> | <span data-ttu-id="eefc6-165">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="eefc6-166">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eefc6-167">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-167">All Transports are enabled.</span></span> | <span data-ttu-id="eefc6-168">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="eefc6-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eefc6-169">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-169">See below.</span></span> | <span data-ttu-id="eefc6-170">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eefc6-171">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-171">See below.</span></span> | <span data-ttu-id="eefc6-172">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="eefc6-173">0</span><span class="sxs-lookup"><span data-stu-id="eefc6-173">0</span></span> | <span data-ttu-id="eefc6-174">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="eefc6-175">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="eefc6-176">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eefc6-177">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-177">Option</span></span> | <span data-ttu-id="eefc6-178">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-178">Default Value</span></span> | <span data-ttu-id="eefc6-179">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eefc6-180">90秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-180">90 seconds</span></span> | <span data-ttu-id="eefc6-181">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eefc6-182">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eefc6-183">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eefc6-184">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-184">Option</span></span> | <span data-ttu-id="eefc6-185">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-185">Default Value</span></span> | <span data-ttu-id="eefc6-186">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eefc6-187">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-187">5 seconds</span></span> | <span data-ttu-id="eefc6-188">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eefc6-189">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eefc6-190">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eefc6-191">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-191">Configure client options</span></span>

<span data-ttu-id="eefc6-192">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eefc6-193">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eefc6-194">ログの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-194">Configure logging</span></span>

<span data-ttu-id="eefc6-195">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eefc6-196">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eefc6-197">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-198">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eefc6-199">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eefc6-200">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eefc6-201">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eefc6-202">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eefc6-203">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eefc6-204">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="eefc6-205">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="eefc6-206">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="eefc6-207">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-207">The following table lists the available log levels.</span></span> <span data-ttu-id="eefc6-208">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="eefc6-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="eefc6-209">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="eefc6-210">String</span><span class="sxs-lookup"><span data-stu-id="eefc6-210">String</span></span>                      | <span data-ttu-id="eefc6-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="eefc6-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="eefc6-212">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="eefc6-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="eefc6-213">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="eefc6-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="eefc6-214">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eefc6-215">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eefc6-216">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eefc6-217">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eefc6-218">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="eefc6-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eefc6-219">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eefc6-220">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eefc6-221">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-221">Configure allowed transports</span></span>

<span data-ttu-id="eefc6-222">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eefc6-223">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eefc6-224">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-224">All transports are enabled by default.</span></span>

<span data-ttu-id="eefc6-225">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eefc6-226">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="eefc6-227">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="eefc6-228">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="eefc6-229">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eefc6-230">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eefc6-231">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-231">Configure bearer authentication</span></span>

<span data-ttu-id="eefc6-232">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eefc6-233">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="eefc6-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eefc6-234">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eefc6-235">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eefc6-236">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eefc6-237">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eefc6-238">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eefc6-239">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eefc6-240">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eefc6-241">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eefc6-242">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-243">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-244">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-244">Option</span></span> | <span data-ttu-id="eefc6-245">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-245">Default value</span></span> | <span data-ttu-id="eefc6-246">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eefc6-247">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-248">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-248">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-249">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-250">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-251">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eefc6-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-252">15 seconds</span></span> | <span data-ttu-id="eefc6-253">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-254">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-255">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-256">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-257">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-257">15 seconds</span></span> | <span data-ttu-id="eefc6-258">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-259">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-260">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="eefc6-261">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eefc6-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-263">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-263">Option</span></span> | <span data-ttu-id="eefc6-264">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-264">Default value</span></span> | <span data-ttu-id="eefc6-265">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eefc6-266">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-267">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-267">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-268">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eefc6-269">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-270">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="eefc6-271">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-272">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-273">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-274">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-275">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-275">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-276">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-276">Option</span></span> | <span data-ttu-id="eefc6-277">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-277">Default value</span></span> | <span data-ttu-id="eefc6-278">説明</span><span class="sxs-lookup"><span data-stu-id="eefc6-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eefc6-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eefc6-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eefc6-280">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-281">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-281">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-282">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-283">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-284">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eefc6-285">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-285">15 seconds</span></span> | <span data-ttu-id="eefc6-286">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-287">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-288">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-289">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="eefc6-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="eefc6-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="eefc6-291">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-292">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-293">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-294">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eefc6-295">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-295">Configure additional options</span></span>

<span data-ttu-id="eefc6-296">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-297">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-298">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-298">.NET Option</span></span> |  <span data-ttu-id="eefc6-299">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-299">Default value</span></span> | <span data-ttu-id="eefc6-300">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eefc6-301">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eefc6-302">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-303">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-304">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eefc6-305">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-305">Empty</span></span> | <span data-ttu-id="eefc6-306">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eefc6-307">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-307">Empty</span></span> | <span data-ttu-id="eefc6-308">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eefc6-309">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-309">Empty</span></span> | <span data-ttu-id="eefc6-310">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="eefc6-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eefc6-311">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-311">5 seconds</span></span> | <span data-ttu-id="eefc6-312">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-312">WebSockets only.</span></span> <span data-ttu-id="eefc6-313">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eefc6-314">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eefc6-315">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-315">Empty</span></span> | <span data-ttu-id="eefc6-316">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eefc6-317">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eefc6-318">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="eefc6-319">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eefc6-320">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eefc6-321">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="eefc6-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eefc6-322">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eefc6-323">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eefc6-324">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eefc6-325">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eefc6-326">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eefc6-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-328">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-328">JavaScript Option</span></span> | <span data-ttu-id="eefc6-329">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-329">Default Value</span></span> | <span data-ttu-id="eefc6-330">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eefc6-331">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="eefc6-332">すべての HTTP 要求と共に送信されるヘッダーのディクショナリ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="eefc6-333">ブラウザーでヘッダーを送信することは、Websocket やストリームでは機能しません <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eefc6-334">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eefc6-335">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-336">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-337">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="eefc6-338">CORS 要求と共に資格情報を送信するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="eefc6-339">Azure App Service は cookie 固定セッションでを使用します。このオプションを正しく動作させるには、このオプションを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="eefc6-340">での CORS の詳細につい SignalR ては、「」を参照してください <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-341">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-341">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-342">Java オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-342">Java Option</span></span> | <span data-ttu-id="eefc6-343">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-343">Default Value</span></span> | <span data-ttu-id="eefc6-344">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eefc6-345">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eefc6-346">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-347">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-348">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eefc6-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eefc6-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eefc6-350">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-350">Empty</span></span> | <span data-ttu-id="eefc6-351">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eefc6-352">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eefc6-353">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eefc6-354">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eefc6-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eefc6-355">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="eefc6-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eefc6-356">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-357">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eefc6-358">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eefc6-359">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="eefc6-360">`AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eefc6-361">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eefc6-362">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eefc6-363">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="eefc6-364">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="eefc6-365">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eefc6-366">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eefc6-367">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="eefc6-368">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="eefc6-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="eefc6-369">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eefc6-370">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-370">MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-371">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eefc6-372">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-373">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eefc6-374">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-374">Configure server options</span></span>

<span data-ttu-id="eefc6-375">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eefc6-376">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-376">Option</span></span> | <span data-ttu-id="eefc6-377">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-377">Default Value</span></span> | <span data-ttu-id="eefc6-378">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="eefc6-379">30 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-379">30 seconds</span></span> | <span data-ttu-id="eefc6-380">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="eefc6-381">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="eefc6-382">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="eefc6-383">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-383">15 seconds</span></span> | <span data-ttu-id="eefc6-384">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eefc6-385">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-386">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-387">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-387">15 seconds</span></span> | <span data-ttu-id="eefc6-388">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eefc6-389">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eefc6-390">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eefc6-391">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="eefc6-391">All installed protocols</span></span> | <span data-ttu-id="eefc6-392">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="eefc6-392">Protocols supported by this hub.</span></span> <span data-ttu-id="eefc6-393">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eefc6-394">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eefc6-395">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="eefc6-396">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="eefc6-397">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="eefc6-398">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-398">32 KB</span></span> | <span data-ttu-id="eefc6-399">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="eefc6-400">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eefc6-401">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eefc6-402">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eefc6-403">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eefc6-404">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eefc6-405">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eefc6-406">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-406">Option</span></span> | <span data-ttu-id="eefc6-407">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-407">Default Value</span></span> | <span data-ttu-id="eefc6-408">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eefc6-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-409">32 KB</span></span> | <span data-ttu-id="eefc6-410">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="eefc6-411">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eefc6-412">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eefc6-413">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="eefc6-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eefc6-414">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-414">32 KB</span></span> | <span data-ttu-id="eefc6-415">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="eefc6-416">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eefc6-417">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-417">All Transports are enabled.</span></span> | <span data-ttu-id="eefc6-418">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="eefc6-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eefc6-419">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-419">See below.</span></span> | <span data-ttu-id="eefc6-420">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eefc6-421">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-421">See below.</span></span> | <span data-ttu-id="eefc6-422">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="eefc6-423">0</span><span class="sxs-lookup"><span data-stu-id="eefc6-423">0</span></span> | <span data-ttu-id="eefc6-424">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="eefc6-425">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="eefc6-426">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eefc6-427">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-427">Option</span></span> | <span data-ttu-id="eefc6-428">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-428">Default Value</span></span> | <span data-ttu-id="eefc6-429">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eefc6-430">90秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-430">90 seconds</span></span> | <span data-ttu-id="eefc6-431">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eefc6-432">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eefc6-433">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eefc6-434">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-434">Option</span></span> | <span data-ttu-id="eefc6-435">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-435">Default Value</span></span> | <span data-ttu-id="eefc6-436">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eefc6-437">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-437">5 seconds</span></span> | <span data-ttu-id="eefc6-438">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eefc6-439">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eefc6-440">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eefc6-441">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-441">Configure client options</span></span>

<span data-ttu-id="eefc6-442">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eefc6-443">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eefc6-444">ログの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-444">Configure logging</span></span>

<span data-ttu-id="eefc6-445">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eefc6-446">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eefc6-447">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-448">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eefc6-449">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eefc6-450">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eefc6-451">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eefc6-452">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eefc6-453">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eefc6-454">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="eefc6-455">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="eefc6-456">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="eefc6-457">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-457">The following table lists the available log levels.</span></span> <span data-ttu-id="eefc6-458">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="eefc6-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="eefc6-459">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="eefc6-460">String</span><span class="sxs-lookup"><span data-stu-id="eefc6-460">String</span></span>                      | <span data-ttu-id="eefc6-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="eefc6-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="eefc6-462">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="eefc6-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="eefc6-463">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="eefc6-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="eefc6-464">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eefc6-465">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eefc6-466">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eefc6-467">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eefc6-468">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="eefc6-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eefc6-469">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eefc6-470">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eefc6-471">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-471">Configure allowed transports</span></span>

<span data-ttu-id="eefc6-472">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eefc6-473">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eefc6-474">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-474">All transports are enabled by default.</span></span>

<span data-ttu-id="eefc6-475">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eefc6-476">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="eefc6-477">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="eefc6-478">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="eefc6-479">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eefc6-480">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eefc6-481">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-481">Configure bearer authentication</span></span>

<span data-ttu-id="eefc6-482">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eefc6-483">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="eefc6-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eefc6-484">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eefc6-485">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eefc6-486">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eefc6-487">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eefc6-488">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eefc6-489">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eefc6-490">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eefc6-491">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eefc6-492">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-493">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-494">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-494">Option</span></span> | <span data-ttu-id="eefc6-495">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-495">Default value</span></span> | <span data-ttu-id="eefc6-496">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eefc6-497">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-498">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-498">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-499">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-500">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-501">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eefc6-502">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-502">15 seconds</span></span> | <span data-ttu-id="eefc6-503">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-504">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-505">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-506">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-507">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-507">15 seconds</span></span> | <span data-ttu-id="eefc6-508">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-509">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-510">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="eefc6-511">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eefc6-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-513">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-513">Option</span></span> | <span data-ttu-id="eefc6-514">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-514">Default value</span></span> | <span data-ttu-id="eefc6-515">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eefc6-516">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-517">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-517">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-518">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eefc6-519">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-520">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="eefc6-521">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-522">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-523">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-524">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-525">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-525">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-526">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-526">Option</span></span> | <span data-ttu-id="eefc6-527">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-527">Default value</span></span> | <span data-ttu-id="eefc6-528">説明</span><span class="sxs-lookup"><span data-stu-id="eefc6-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eefc6-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eefc6-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eefc6-530">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-531">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-531">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-532">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-533">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-534">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eefc6-535">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-535">15 seconds</span></span> | <span data-ttu-id="eefc6-536">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-537">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-538">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-539">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="eefc6-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="eefc6-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="eefc6-541">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-542">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-543">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-544">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eefc6-545">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-545">Configure additional options</span></span>

<span data-ttu-id="eefc6-546">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-547">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-548">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-548">.NET Option</span></span> |  <span data-ttu-id="eefc6-549">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-549">Default value</span></span> | <span data-ttu-id="eefc6-550">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eefc6-551">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eefc6-552">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-553">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-554">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eefc6-555">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-555">Empty</span></span> | <span data-ttu-id="eefc6-556">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eefc6-557">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-557">Empty</span></span> | <span data-ttu-id="eefc6-558">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eefc6-559">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-559">Empty</span></span> | <span data-ttu-id="eefc6-560">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="eefc6-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eefc6-561">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-561">5 seconds</span></span> | <span data-ttu-id="eefc6-562">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-562">WebSockets only.</span></span> <span data-ttu-id="eefc6-563">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eefc6-564">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eefc6-565">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-565">Empty</span></span> | <span data-ttu-id="eefc6-566">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eefc6-567">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eefc6-568">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="eefc6-569">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eefc6-570">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eefc6-571">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="eefc6-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eefc6-572">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eefc6-573">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eefc6-574">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eefc6-575">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eefc6-576">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eefc6-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-578">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-578">JavaScript Option</span></span> | <span data-ttu-id="eefc6-579">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-579">Default Value</span></span> | <span data-ttu-id="eefc6-580">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eefc6-581">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eefc6-582">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eefc6-583">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-584">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-585">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-586">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-586">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-587">Java オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-587">Java Option</span></span> | <span data-ttu-id="eefc6-588">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-588">Default Value</span></span> | <span data-ttu-id="eefc6-589">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eefc6-590">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eefc6-591">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-592">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-593">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eefc6-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eefc6-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eefc6-595">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-595">Empty</span></span> | <span data-ttu-id="eefc6-596">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eefc6-597">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eefc6-598">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eefc6-599">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eefc6-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eefc6-600">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="eefc6-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eefc6-601">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-602">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eefc6-603">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eefc6-604">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="eefc6-605">`AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eefc6-606">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eefc6-607">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eefc6-608">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="eefc6-609">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="eefc6-610">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eefc6-611">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eefc6-612">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="eefc6-613">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="eefc6-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="eefc6-614">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eefc6-615">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-615">MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-616">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eefc6-617">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-618">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eefc6-619">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-619">Configure server options</span></span>

<span data-ttu-id="eefc6-620">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eefc6-621">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-621">Option</span></span> | <span data-ttu-id="eefc6-622">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-622">Default Value</span></span> | <span data-ttu-id="eefc6-623">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="eefc6-624">30 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-624">30 seconds</span></span> | <span data-ttu-id="eefc6-625">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="eefc6-626">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="eefc6-627">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="eefc6-628">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-628">15 seconds</span></span> | <span data-ttu-id="eefc6-629">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eefc6-630">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-631">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-632">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-632">15 seconds</span></span> | <span data-ttu-id="eefc6-633">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eefc6-634">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eefc6-635">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eefc6-636">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="eefc6-636">All installed protocols</span></span> | <span data-ttu-id="eefc6-637">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="eefc6-637">Protocols supported by this hub.</span></span> <span data-ttu-id="eefc6-638">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eefc6-639">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eefc6-640">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="eefc6-641">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="eefc6-642">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="eefc6-643">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-643">32 KB</span></span> | <span data-ttu-id="eefc6-644">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="eefc6-645">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eefc6-646">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eefc6-647">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eefc6-648">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eefc6-649">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eefc6-650">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eefc6-651">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-651">Option</span></span> | <span data-ttu-id="eefc6-652">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-652">Default Value</span></span> | <span data-ttu-id="eefc6-653">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eefc6-654">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-654">32 KB</span></span> | <span data-ttu-id="eefc6-655">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="eefc6-656">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eefc6-657">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eefc6-658">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="eefc6-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eefc6-659">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-659">32 KB</span></span> | <span data-ttu-id="eefc6-660">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="eefc6-661">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eefc6-662">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-662">All Transports are enabled.</span></span> | <span data-ttu-id="eefc6-663">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="eefc6-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eefc6-664">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-664">See below.</span></span> | <span data-ttu-id="eefc6-665">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eefc6-666">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-666">See below.</span></span> | <span data-ttu-id="eefc6-667">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="eefc6-668">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eefc6-669">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-669">Option</span></span> | <span data-ttu-id="eefc6-670">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-670">Default Value</span></span> | <span data-ttu-id="eefc6-671">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eefc6-672">90秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-672">90 seconds</span></span> | <span data-ttu-id="eefc6-673">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eefc6-674">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eefc6-675">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eefc6-676">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-676">Option</span></span> | <span data-ttu-id="eefc6-677">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-677">Default Value</span></span> | <span data-ttu-id="eefc6-678">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eefc6-679">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-679">5 seconds</span></span> | <span data-ttu-id="eefc6-680">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eefc6-681">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eefc6-682">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eefc6-683">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-683">Configure client options</span></span>

<span data-ttu-id="eefc6-684">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eefc6-685">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eefc6-686">ログの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-686">Configure logging</span></span>

<span data-ttu-id="eefc6-687">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eefc6-688">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eefc6-689">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-690">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eefc6-691">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eefc6-692">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eefc6-693">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eefc6-694">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eefc6-695">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eefc6-696">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="eefc6-697">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="eefc6-698">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="eefc6-699">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-699">The following table lists the available log levels.</span></span> <span data-ttu-id="eefc6-700">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="eefc6-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="eefc6-701">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="eefc6-702">String</span><span class="sxs-lookup"><span data-stu-id="eefc6-702">String</span></span>                      | <span data-ttu-id="eefc6-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="eefc6-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="eefc6-704">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="eefc6-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="eefc6-705">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="eefc6-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="eefc6-706">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eefc6-707">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eefc6-708">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eefc6-709">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eefc6-710">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="eefc6-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eefc6-711">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eefc6-712">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eefc6-713">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-713">Configure allowed transports</span></span>

<span data-ttu-id="eefc6-714">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eefc6-715">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eefc6-716">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-716">All transports are enabled by default.</span></span>

<span data-ttu-id="eefc6-717">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eefc6-718">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="eefc6-719">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="eefc6-720">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="eefc6-721">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eefc6-722">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eefc6-723">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-723">Configure bearer authentication</span></span>

<span data-ttu-id="eefc6-724">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eefc6-725">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="eefc6-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eefc6-726">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eefc6-727">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eefc6-728">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eefc6-729">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eefc6-730">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eefc6-731">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eefc6-732">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eefc6-733">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eefc6-734">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-735">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-736">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-736">Option</span></span> | <span data-ttu-id="eefc6-737">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-737">Default value</span></span> | <span data-ttu-id="eefc6-738">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eefc6-739">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-740">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-740">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-741">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-742">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-743">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eefc6-744">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-744">15 seconds</span></span> | <span data-ttu-id="eefc6-745">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-746">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-747">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-748">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-749">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-749">15 seconds</span></span> | <span data-ttu-id="eefc6-750">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-751">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-752">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="eefc6-753">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eefc6-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-755">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-755">Option</span></span> | <span data-ttu-id="eefc6-756">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-756">Default value</span></span> | <span data-ttu-id="eefc6-757">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eefc6-758">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-759">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-759">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-760">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eefc6-761">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-762">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="eefc6-763">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-764">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-765">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-766">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-767">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-767">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-768">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-768">Option</span></span> | <span data-ttu-id="eefc6-769">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-769">Default value</span></span> | <span data-ttu-id="eefc6-770">説明</span><span class="sxs-lookup"><span data-stu-id="eefc6-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eefc6-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eefc6-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eefc6-772">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-773">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-773">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-774">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-775">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-776">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eefc6-777">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-777">15 seconds</span></span> | <span data-ttu-id="eefc6-778">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-779">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-780">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-781">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="eefc6-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="eefc6-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="eefc6-783">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-784">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-785">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-786">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eefc6-787">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-787">Configure additional options</span></span>

<span data-ttu-id="eefc6-788">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-789">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-790">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-790">.NET Option</span></span> |  <span data-ttu-id="eefc6-791">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-791">Default value</span></span> | <span data-ttu-id="eefc6-792">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eefc6-793">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eefc6-794">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-795">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-796">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eefc6-797">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-797">Empty</span></span> | <span data-ttu-id="eefc6-798">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eefc6-799">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-799">Empty</span></span> | <span data-ttu-id="eefc6-800">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eefc6-801">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-801">Empty</span></span> | <span data-ttu-id="eefc6-802">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="eefc6-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eefc6-803">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-803">5 seconds</span></span> | <span data-ttu-id="eefc6-804">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-804">WebSockets only.</span></span> <span data-ttu-id="eefc6-805">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eefc6-806">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eefc6-807">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-807">Empty</span></span> | <span data-ttu-id="eefc6-808">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eefc6-809">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eefc6-810">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="eefc6-811">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eefc6-812">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eefc6-813">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="eefc6-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eefc6-814">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eefc6-815">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eefc6-816">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eefc6-817">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eefc6-818">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eefc6-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-820">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-820">JavaScript Option</span></span> | <span data-ttu-id="eefc6-821">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-821">Default Value</span></span> | <span data-ttu-id="eefc6-822">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eefc6-823">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eefc6-824">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eefc6-825">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-826">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-827">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-828">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-828">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-829">Java オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-829">Java Option</span></span> | <span data-ttu-id="eefc6-830">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-830">Default Value</span></span> | <span data-ttu-id="eefc6-831">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eefc6-832">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eefc6-833">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-834">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-835">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eefc6-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eefc6-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eefc6-837">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-837">Empty</span></span> | <span data-ttu-id="eefc6-838">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eefc6-839">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eefc6-840">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eefc6-841">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eefc6-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eefc6-842">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="eefc6-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eefc6-843">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-844">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eefc6-845">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eefc6-846">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドに[Add を SignalR 追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)した後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eefc6-847">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eefc6-848">そのオブジェクトの [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eefc6-849">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="eefc6-850">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="eefc6-851">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eefc6-852">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eefc6-853">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eefc6-854">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-854">MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-855">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eefc6-856">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-857">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eefc6-858">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-858">Configure server options</span></span>

<span data-ttu-id="eefc6-859">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eefc6-860">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-860">Option</span></span> | <span data-ttu-id="eefc6-861">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-861">Default Value</span></span> | <span data-ttu-id="eefc6-862">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="eefc6-863">30 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-863">30 seconds</span></span> | <span data-ttu-id="eefc6-864">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="eefc6-865">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="eefc6-866">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="eefc6-867">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-867">15 seconds</span></span> | <span data-ttu-id="eefc6-868">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eefc6-869">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-870">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-871">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-871">15 seconds</span></span> | <span data-ttu-id="eefc6-872">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eefc6-873">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eefc6-874">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eefc6-875">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="eefc6-875">All installed protocols</span></span> | <span data-ttu-id="eefc6-876">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="eefc6-876">Protocols supported by this hub.</span></span> <span data-ttu-id="eefc6-877">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eefc6-878">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eefc6-879">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="eefc6-880">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eefc6-881">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eefc6-882">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eefc6-883">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eefc6-884">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eefc6-885">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eefc6-886">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-886">Option</span></span> | <span data-ttu-id="eefc6-887">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-887">Default Value</span></span> | <span data-ttu-id="eefc6-888">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eefc6-889">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-889">32 KB</span></span> | <span data-ttu-id="eefc6-890">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="eefc6-891">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eefc6-892">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eefc6-893">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="eefc6-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eefc6-894">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-894">32 KB</span></span> | <span data-ttu-id="eefc6-895">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="eefc6-896">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eefc6-897">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-897">All Transports are enabled.</span></span> | <span data-ttu-id="eefc6-898">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="eefc6-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eefc6-899">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-899">See below.</span></span> | <span data-ttu-id="eefc6-900">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eefc6-901">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-901">See below.</span></span> | <span data-ttu-id="eefc6-902">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="eefc6-903">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eefc6-904">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-904">Option</span></span> | <span data-ttu-id="eefc6-905">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-905">Default Value</span></span> | <span data-ttu-id="eefc6-906">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eefc6-907">90秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-907">90 seconds</span></span> | <span data-ttu-id="eefc6-908">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eefc6-909">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eefc6-910">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eefc6-911">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-911">Option</span></span> | <span data-ttu-id="eefc6-912">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-912">Default Value</span></span> | <span data-ttu-id="eefc6-913">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eefc6-914">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-914">5 seconds</span></span> | <span data-ttu-id="eefc6-915">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eefc6-916">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eefc6-917">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eefc6-918">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-918">Configure client options</span></span>

<span data-ttu-id="eefc6-919">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eefc6-920">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eefc6-921">ログの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-921">Configure logging</span></span>

<span data-ttu-id="eefc6-922">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eefc6-923">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eefc6-924">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-925">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eefc6-926">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eefc6-927">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eefc6-928">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eefc6-929">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eefc6-930">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eefc6-931">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eefc6-932">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eefc6-933">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eefc6-934">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eefc6-935">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eefc6-936">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="eefc6-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eefc6-937">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eefc6-938">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eefc6-939">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-939">Configure allowed transports</span></span>

<span data-ttu-id="eefc6-940">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eefc6-941">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eefc6-942">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-942">All transports are enabled by default.</span></span>

<span data-ttu-id="eefc6-943">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eefc6-944">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="eefc6-945">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eefc6-946">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-946">Configure bearer authentication</span></span>

<span data-ttu-id="eefc6-947">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eefc6-948">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="eefc6-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eefc6-949">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eefc6-950">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eefc6-951">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eefc6-952">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eefc6-953">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eefc6-954">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eefc6-955">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eefc6-956">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eefc6-957">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-958">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-959">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-959">Option</span></span> | <span data-ttu-id="eefc6-960">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-960">Default value</span></span> | <span data-ttu-id="eefc6-961">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eefc6-962">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-963">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-963">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-964">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-965">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-966">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eefc6-967">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-967">15 seconds</span></span> | <span data-ttu-id="eefc6-968">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-969">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-970">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-971">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-972">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-972">15 seconds</span></span> | <span data-ttu-id="eefc6-973">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-974">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-975">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="eefc6-976">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eefc6-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-978">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-978">Option</span></span> | <span data-ttu-id="eefc6-979">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-979">Default value</span></span> | <span data-ttu-id="eefc6-980">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eefc6-981">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-982">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-982">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-983">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eefc6-984">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-985">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="eefc6-986">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-987">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-988">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-989">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-990">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-990">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-991">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-991">Option</span></span> | <span data-ttu-id="eefc6-992">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-992">Default value</span></span> | <span data-ttu-id="eefc6-993">説明</span><span class="sxs-lookup"><span data-stu-id="eefc6-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eefc6-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eefc6-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eefc6-995">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-996">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-996">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-997">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-998">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-999">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eefc6-1000">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1000">15 seconds</span></span> | <span data-ttu-id="eefc6-1001">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-1002">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-1003">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-1004">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="eefc6-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="eefc6-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="eefc6-1006">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-1007">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="eefc6-1008">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="eefc6-1009">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eefc6-1010">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-1010">Configure additional options</span></span>

<span data-ttu-id="eefc6-1011">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-1013">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1013">.NET Option</span></span> |  <span data-ttu-id="eefc6-1014">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1014">Default value</span></span> | <span data-ttu-id="eefc6-1015">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eefc6-1016">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eefc6-1017">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-1018">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-1019">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eefc6-1020">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1020">Empty</span></span> | <span data-ttu-id="eefc6-1021">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eefc6-1022">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1022">Empty</span></span> | <span data-ttu-id="eefc6-1023">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eefc6-1024">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1024">Empty</span></span> | <span data-ttu-id="eefc6-1025">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eefc6-1026">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1026">5 seconds</span></span> | <span data-ttu-id="eefc6-1027">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1027">WebSockets only.</span></span> <span data-ttu-id="eefc6-1028">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eefc6-1029">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eefc6-1030">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1030">Empty</span></span> | <span data-ttu-id="eefc6-1031">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eefc6-1032">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eefc6-1033">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="eefc6-1034">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eefc6-1035">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eefc6-1036">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="eefc6-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eefc6-1037">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eefc6-1038">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eefc6-1039">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eefc6-1040">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eefc6-1041">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eefc6-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-1043">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1043">JavaScript Option</span></span> | <span data-ttu-id="eefc6-1044">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1044">Default Value</span></span> | <span data-ttu-id="eefc6-1045">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eefc6-1046">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eefc6-1047">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eefc6-1048">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-1049">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-1050">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-1051">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-1052">Java オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1052">Java Option</span></span> | <span data-ttu-id="eefc6-1053">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1053">Default Value</span></span> | <span data-ttu-id="eefc6-1054">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eefc6-1055">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eefc6-1056">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-1057">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-1058">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eefc6-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eefc6-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eefc6-1060">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1060">Empty</span></span> | <span data-ttu-id="eefc6-1061">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eefc6-1062">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eefc6-1063">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eefc6-1064">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eefc6-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eefc6-1065">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="eefc6-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="eefc6-1066">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-1067">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="eefc6-1068">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="eefc6-1069">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドに[Add を SignalR 追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)した後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="eefc6-1070">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="eefc6-1071">そのオブジェクトの [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="eefc6-1072">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="eefc6-1073">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="eefc6-1074">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="eefc6-1075">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="eefc6-1076">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="eefc6-1077">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1077">MessagePack serialization options</span></span>

<span data-ttu-id="eefc6-1078">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="eefc6-1079">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-1080">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="eefc6-1081">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-1081">Configure server options</span></span>

<span data-ttu-id="eefc6-1082">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="eefc6-1083">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1083">Option</span></span> | <span data-ttu-id="eefc6-1084">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1084">Default Value</span></span> | <span data-ttu-id="eefc6-1085">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="eefc6-1086">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1086">15 seconds</span></span> | <span data-ttu-id="eefc6-1087">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="eefc6-1088">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-1089">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="eefc6-1090">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1090">15 seconds</span></span> | <span data-ttu-id="eefc6-1091">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="eefc6-1092">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="eefc6-1093">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="eefc6-1094">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="eefc6-1094">All installed protocols</span></span> | <span data-ttu-id="eefc6-1095">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="eefc6-1096">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="eefc6-1097">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="eefc6-1098">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="eefc6-1099">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="eefc6-1100">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="eefc6-1101">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="eefc6-1102">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="eefc6-1103">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="eefc6-1104">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="eefc6-1105">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1105">Option</span></span> | <span data-ttu-id="eefc6-1106">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1106">Default Value</span></span> | <span data-ttu-id="eefc6-1107">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="eefc6-1108">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-1108">32 KB</span></span> | <span data-ttu-id="eefc6-1109">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="eefc6-1110">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="eefc6-1111">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="eefc6-1112">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="eefc6-1113">32 KB</span><span class="sxs-lookup"><span data-stu-id="eefc6-1113">32 KB</span></span> | <span data-ttu-id="eefc6-1114">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="eefc6-1115">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="eefc6-1116">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1116">All Transports are enabled.</span></span> | <span data-ttu-id="eefc6-1117">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="eefc6-1118">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1118">See below.</span></span> | <span data-ttu-id="eefc6-1119">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="eefc6-1120">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1120">See below.</span></span> | <span data-ttu-id="eefc6-1121">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="eefc6-1122">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="eefc6-1123">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1123">Option</span></span> | <span data-ttu-id="eefc6-1124">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1124">Default Value</span></span> | <span data-ttu-id="eefc6-1125">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="eefc6-1126">90秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1126">90 seconds</span></span> | <span data-ttu-id="eefc6-1127">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="eefc6-1128">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="eefc6-1129">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="eefc6-1130">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1130">Option</span></span> | <span data-ttu-id="eefc6-1131">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1131">Default Value</span></span> | <span data-ttu-id="eefc6-1132">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="eefc6-1133">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1133">5 seconds</span></span> | <span data-ttu-id="eefc6-1134">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="eefc6-1135">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="eefc6-1136">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="eefc6-1137">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-1137">Configure client options</span></span>

<span data-ttu-id="eefc6-1138">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="eefc6-1139">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="eefc6-1140">ログの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-1140">Configure logging</span></span>

<span data-ttu-id="eefc6-1141">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="eefc6-1142">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="eefc6-1143">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="eefc6-1144">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="eefc6-1145">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="eefc6-1146">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="eefc6-1147">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="eefc6-1148">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="eefc6-1149">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="eefc6-1150">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="eefc6-1151">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="eefc6-1152">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="eefc6-1153">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="eefc6-1154">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="eefc6-1155">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="eefc6-1156">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="eefc6-1157">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="eefc6-1158">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-1158">Configure allowed transports</span></span>

<span data-ttu-id="eefc6-1159">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="eefc6-1160">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="eefc6-1161">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="eefc6-1162">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="eefc6-1163">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="eefc6-1164">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-1164">Configure bearer authentication</span></span>

<span data-ttu-id="eefc6-1165">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="eefc6-1166">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="eefc6-1167">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="eefc6-1168">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="eefc6-1169">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="eefc6-1170">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="eefc6-1171">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="eefc6-1172">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="eefc6-1173">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="eefc6-1174">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="eefc6-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="eefc6-1175">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-1177">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1177">Option</span></span> | <span data-ttu-id="eefc6-1178">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1178">Default value</span></span> | <span data-ttu-id="eefc6-1179">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="eefc6-1180">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-1181">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1181">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-1182">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-1183">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-1184">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="eefc6-1185">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1185">15 seconds</span></span> | <span data-ttu-id="eefc6-1186">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-1187">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="eefc6-1188">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-1189">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="eefc6-1190">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="eefc6-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-1192">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1192">Option</span></span> | <span data-ttu-id="eefc6-1193">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1193">Default value</span></span> | <span data-ttu-id="eefc6-1194">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="eefc6-1195">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-1196">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1196">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-1197">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="eefc6-1198">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-1199">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-1200">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-1201">オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1201">Option</span></span> | <span data-ttu-id="eefc6-1202">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1202">Default value</span></span> | <span data-ttu-id="eefc6-1203">説明</span><span class="sxs-lookup"><span data-stu-id="eefc6-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="eefc6-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="eefc6-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="eefc6-1205">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="eefc6-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="eefc6-1206">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1206">Timeout for server activity.</span></span> <span data-ttu-id="eefc6-1207">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-1208">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="eefc6-1209">推奨値は、サーバーの値の少なくとも2倍の数で、 `KeepAliveInterval` ping が到着するまでの時間を確保します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="eefc6-1210">15 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1210">15 seconds</span></span> | <span data-ttu-id="eefc6-1211">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="eefc6-1212">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="eefc6-1213">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="eefc6-1214">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="eefc6-1215">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="eefc6-1215">Configure additional options</span></span>

<span data-ttu-id="eefc6-1216">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="eefc6-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="eefc6-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="eefc6-1218">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1218">.NET Option</span></span> |  <span data-ttu-id="eefc6-1219">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1219">Default value</span></span> | <span data-ttu-id="eefc6-1220">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="eefc6-1221">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="eefc6-1222">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-1223">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-1224">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="eefc6-1225">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1225">Empty</span></span> | <span data-ttu-id="eefc6-1226">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="eefc6-1227">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1227">Empty</span></span> | <span data-ttu-id="eefc6-1228">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="eefc6-1229">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1229">Empty</span></span> | <span data-ttu-id="eefc6-1230">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="eefc6-1231">5 秒</span><span class="sxs-lookup"><span data-stu-id="eefc6-1231">5 seconds</span></span> | <span data-ttu-id="eefc6-1232">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1232">WebSockets only.</span></span> <span data-ttu-id="eefc6-1233">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="eefc6-1234">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="eefc6-1235">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1235">Empty</span></span> | <span data-ttu-id="eefc6-1236">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="eefc6-1237">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="eefc6-1238">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="eefc6-1239">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="eefc6-1240">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="eefc6-1241">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="eefc6-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="eefc6-1242">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="eefc6-1243">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="eefc6-1244">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="eefc6-1245">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="eefc6-1246">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="eefc6-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="eefc6-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="eefc6-1248">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1248">JavaScript Option</span></span> | <span data-ttu-id="eefc6-1249">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1249">Default Value</span></span> | <span data-ttu-id="eefc6-1250">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="eefc6-1251">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="eefc6-1252">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="eefc6-1253">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-1254">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-1255">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="eefc6-1256">Java</span><span class="sxs-lookup"><span data-stu-id="eefc6-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="eefc6-1257">Java オプション</span><span class="sxs-lookup"><span data-stu-id="eefc6-1257">Java Option</span></span> | <span data-ttu-id="eefc6-1258">既定値</span><span class="sxs-lookup"><span data-stu-id="eefc6-1258">Default Value</span></span> | <span data-ttu-id="eefc6-1259">[説明]</span><span class="sxs-lookup"><span data-stu-id="eefc6-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="eefc6-1260">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="eefc6-1261">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="eefc6-1262">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="eefc6-1263">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="eefc6-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="eefc6-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="eefc6-1265">空</span><span class="sxs-lookup"><span data-stu-id="eefc6-1265">Empty</span></span> | <span data-ttu-id="eefc6-1266">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="eefc6-1267">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="eefc6-1268">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="eefc6-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="eefc6-1269">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="eefc6-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="eefc6-1270">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="eefc6-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
