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
ms.openlocfilehash: fc0e6398884bb5c3b806a587a8a361d7f279461f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625558"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="93fbc-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="93fbc-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-105">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="93fbc-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="93fbc-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="93fbc-108">`AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="93fbc-109">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="93fbc-110">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="93fbc-111">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="93fbc-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="93fbc-113">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="93fbc-114">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="93fbc-115">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="93fbc-116">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="93fbc-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="93fbc-117">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="93fbc-118">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-118">MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-119">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="93fbc-120">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-121">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="93fbc-122">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-122">Configure server options</span></span>

<span data-ttu-id="93fbc-123">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="93fbc-124">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-124">Option</span></span> | <span data-ttu-id="93fbc-125">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-125">Default Value</span></span> | <span data-ttu-id="93fbc-126">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="93fbc-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-127">30 seconds</span></span> | <span data-ttu-id="93fbc-128">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="93fbc-129">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="93fbc-130">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="93fbc-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-131">15 seconds</span></span> | <span data-ttu-id="93fbc-132">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="93fbc-133">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-134">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-135">15 seconds</span></span> | <span data-ttu-id="93fbc-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="93fbc-137">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="93fbc-138">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="93fbc-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="93fbc-139">All installed protocols</span></span> | <span data-ttu-id="93fbc-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="93fbc-140">Protocols supported by this hub.</span></span> <span data-ttu-id="93fbc-141">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="93fbc-142">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="93fbc-143">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="93fbc-144">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="93fbc-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="93fbc-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-146">32 KB</span></span> | <span data-ttu-id="93fbc-147">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="93fbc-148">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="93fbc-149">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="93fbc-150">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="93fbc-151">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="93fbc-152">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="93fbc-153">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="93fbc-154">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-154">Option</span></span> | <span data-ttu-id="93fbc-155">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-155">Default Value</span></span> | <span data-ttu-id="93fbc-156">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="93fbc-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-157">32 KB</span></span> | <span data-ttu-id="93fbc-158">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="93fbc-159">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="93fbc-160">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="93fbc-161">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="93fbc-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="93fbc-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-162">32 KB</span></span> | <span data-ttu-id="93fbc-163">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="93fbc-164">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="93fbc-165">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-165">All Transports are enabled.</span></span> | <span data-ttu-id="93fbc-166">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="93fbc-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="93fbc-167">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-167">See below.</span></span> | <span data-ttu-id="93fbc-168">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="93fbc-169">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-169">See below.</span></span> | <span data-ttu-id="93fbc-170">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="93fbc-171">0</span><span class="sxs-lookup"><span data-stu-id="93fbc-171">0</span></span> | <span data-ttu-id="93fbc-172">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="93fbc-173">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="93fbc-174">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="93fbc-175">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-175">Option</span></span> | <span data-ttu-id="93fbc-176">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-176">Default Value</span></span> | <span data-ttu-id="93fbc-177">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="93fbc-178">90秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-178">90 seconds</span></span> | <span data-ttu-id="93fbc-179">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="93fbc-180">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="93fbc-181">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="93fbc-182">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-182">Option</span></span> | <span data-ttu-id="93fbc-183">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-183">Default Value</span></span> | <span data-ttu-id="93fbc-184">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="93fbc-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-185">5 seconds</span></span> | <span data-ttu-id="93fbc-186">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="93fbc-187">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="93fbc-188">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="93fbc-189">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-189">Configure client options</span></span>

<span data-ttu-id="93fbc-190">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="93fbc-191">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="93fbc-192">ログの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-192">Configure logging</span></span>

<span data-ttu-id="93fbc-193">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="93fbc-194">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="93fbc-195">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-196">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="93fbc-197">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="93fbc-198">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="93fbc-199">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="93fbc-200">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="93fbc-201">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="93fbc-202">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="93fbc-203">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="93fbc-204">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="93fbc-205">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-205">The following table lists the available log levels.</span></span> <span data-ttu-id="93fbc-206">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="93fbc-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="93fbc-207">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="93fbc-208">String</span><span class="sxs-lookup"><span data-stu-id="93fbc-208">String</span></span>                      | <span data-ttu-id="93fbc-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="93fbc-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="93fbc-210">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="93fbc-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="93fbc-211">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="93fbc-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="93fbc-212">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="93fbc-213">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="93fbc-214">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="93fbc-215">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="93fbc-216">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="93fbc-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="93fbc-217">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="93fbc-218">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="93fbc-219">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-219">Configure allowed transports</span></span>

<span data-ttu-id="93fbc-220">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="93fbc-221">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="93fbc-222">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-222">All transports are enabled by default.</span></span>

<span data-ttu-id="93fbc-223">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="93fbc-224">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="93fbc-225">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="93fbc-226">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="93fbc-227">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="93fbc-228">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="93fbc-229">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-229">Configure bearer authentication</span></span>

<span data-ttu-id="93fbc-230">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="93fbc-231">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="93fbc-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="93fbc-232">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="93fbc-233">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="93fbc-234">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="93fbc-235">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="93fbc-236">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="93fbc-237">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="93fbc-238">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="93fbc-239">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="93fbc-240">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-241">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-242">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-242">Option</span></span> | <span data-ttu-id="93fbc-243">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-243">Default value</span></span> | <span data-ttu-id="93fbc-244">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="93fbc-245">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-246">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-246">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-247">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-248">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-249">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="93fbc-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-250">15 seconds</span></span> | <span data-ttu-id="93fbc-251">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-252">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-253">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-254">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-255">15 seconds</span></span> | <span data-ttu-id="93fbc-256">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-257">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-258">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="93fbc-259">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="93fbc-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-261">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-261">Option</span></span> | <span data-ttu-id="93fbc-262">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-262">Default value</span></span> | <span data-ttu-id="93fbc-263">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="93fbc-264">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-265">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-265">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-266">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="93fbc-267">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-268">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="93fbc-269">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-270">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-271">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-272">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-273">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-273">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-274">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-274">Option</span></span> | <span data-ttu-id="93fbc-275">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-275">Default value</span></span> | <span data-ttu-id="93fbc-276">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="93fbc-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="93fbc-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="93fbc-278">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-279">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-279">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-280">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-281">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-282">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="93fbc-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-283">15 seconds</span></span> | <span data-ttu-id="93fbc-284">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-285">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-286">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-287">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="93fbc-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="93fbc-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="93fbc-289">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-290">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-291">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-292">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="93fbc-293">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-293">Configure additional options</span></span>

<span data-ttu-id="93fbc-294">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-295">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-296">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-296">.NET Option</span></span> |  <span data-ttu-id="93fbc-297">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-297">Default value</span></span> | <span data-ttu-id="93fbc-298">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="93fbc-299">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="93fbc-300">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-301">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-302">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="93fbc-303">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-303">Empty</span></span> | <span data-ttu-id="93fbc-304">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="93fbc-305">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-305">Empty</span></span> | <span data-ttu-id="93fbc-306">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="93fbc-307">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-307">Empty</span></span> | <span data-ttu-id="93fbc-308">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="93fbc-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="93fbc-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-309">5 seconds</span></span> | <span data-ttu-id="93fbc-310">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-310">WebSockets only.</span></span> <span data-ttu-id="93fbc-311">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="93fbc-312">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="93fbc-313">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-313">Empty</span></span> | <span data-ttu-id="93fbc-314">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="93fbc-315">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="93fbc-316">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="93fbc-317">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="93fbc-318">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="93fbc-319">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="93fbc-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="93fbc-320">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="93fbc-321">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="93fbc-322">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="93fbc-323">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="93fbc-324">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="93fbc-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-326">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-326">JavaScript Option</span></span> | <span data-ttu-id="93fbc-327">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-327">Default Value</span></span> | <span data-ttu-id="93fbc-328">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="93fbc-329">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="93fbc-330">すべての HTTP 要求と共に送信されるヘッダーのディクショナリ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="93fbc-331">ブラウザーでヘッダーを送信することは、Websocket やストリームでは機能しません <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="93fbc-332">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="93fbc-333">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-334">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-335">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="93fbc-336">CORS 要求と共に資格情報を送信するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="93fbc-337">Azure App Service は cookie 固定セッションでを使用します。このオプションを正しく動作させるには、このオプションを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="93fbc-338">での CORS の詳細につい SignalR ては、「」を参照してください <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-339">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-339">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-340">Java オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-340">Java Option</span></span> | <span data-ttu-id="93fbc-341">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-341">Default Value</span></span> | <span data-ttu-id="93fbc-342">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="93fbc-343">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="93fbc-344">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-345">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-346">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="93fbc-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="93fbc-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="93fbc-348">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-348">Empty</span></span> | <span data-ttu-id="93fbc-349">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="93fbc-350">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="93fbc-351">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="93fbc-352">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="93fbc-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="93fbc-353">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="93fbc-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="93fbc-354">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-355">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="93fbc-356">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="93fbc-357">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="93fbc-358">`AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="93fbc-359">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="93fbc-360">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="93fbc-361">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="93fbc-362">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="93fbc-363">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="93fbc-364">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="93fbc-365">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="93fbc-366">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="93fbc-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="93fbc-367">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="93fbc-368">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-368">MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-369">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="93fbc-370">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-371">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="93fbc-372">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-372">Configure server options</span></span>

<span data-ttu-id="93fbc-373">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="93fbc-374">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-374">Option</span></span> | <span data-ttu-id="93fbc-375">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-375">Default Value</span></span> | <span data-ttu-id="93fbc-376">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="93fbc-377">30 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-377">30 seconds</span></span> | <span data-ttu-id="93fbc-378">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="93fbc-379">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="93fbc-380">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="93fbc-381">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-381">15 seconds</span></span> | <span data-ttu-id="93fbc-382">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="93fbc-383">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-384">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-385">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-385">15 seconds</span></span> | <span data-ttu-id="93fbc-386">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="93fbc-387">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="93fbc-388">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="93fbc-389">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="93fbc-389">All installed protocols</span></span> | <span data-ttu-id="93fbc-390">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="93fbc-390">Protocols supported by this hub.</span></span> <span data-ttu-id="93fbc-391">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="93fbc-392">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="93fbc-393">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="93fbc-394">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="93fbc-395">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="93fbc-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-396">32 KB</span></span> | <span data-ttu-id="93fbc-397">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="93fbc-398">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="93fbc-399">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="93fbc-400">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="93fbc-401">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="93fbc-402">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="93fbc-403">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="93fbc-404">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-404">Option</span></span> | <span data-ttu-id="93fbc-405">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-405">Default Value</span></span> | <span data-ttu-id="93fbc-406">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="93fbc-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-407">32 KB</span></span> | <span data-ttu-id="93fbc-408">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="93fbc-409">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="93fbc-410">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="93fbc-411">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="93fbc-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="93fbc-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-412">32 KB</span></span> | <span data-ttu-id="93fbc-413">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="93fbc-414">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="93fbc-415">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-415">All Transports are enabled.</span></span> | <span data-ttu-id="93fbc-416">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="93fbc-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="93fbc-417">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-417">See below.</span></span> | <span data-ttu-id="93fbc-418">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="93fbc-419">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-419">See below.</span></span> | <span data-ttu-id="93fbc-420">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="93fbc-421">0</span><span class="sxs-lookup"><span data-stu-id="93fbc-421">0</span></span> | <span data-ttu-id="93fbc-422">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="93fbc-423">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="93fbc-424">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="93fbc-425">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-425">Option</span></span> | <span data-ttu-id="93fbc-426">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-426">Default Value</span></span> | <span data-ttu-id="93fbc-427">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="93fbc-428">90秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-428">90 seconds</span></span> | <span data-ttu-id="93fbc-429">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="93fbc-430">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="93fbc-431">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="93fbc-432">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-432">Option</span></span> | <span data-ttu-id="93fbc-433">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-433">Default Value</span></span> | <span data-ttu-id="93fbc-434">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="93fbc-435">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-435">5 seconds</span></span> | <span data-ttu-id="93fbc-436">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="93fbc-437">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="93fbc-438">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="93fbc-439">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-439">Configure client options</span></span>

<span data-ttu-id="93fbc-440">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="93fbc-441">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="93fbc-442">ログの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-442">Configure logging</span></span>

<span data-ttu-id="93fbc-443">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="93fbc-444">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="93fbc-445">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-446">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="93fbc-447">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="93fbc-448">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="93fbc-449">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="93fbc-450">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="93fbc-451">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="93fbc-452">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="93fbc-453">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="93fbc-454">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="93fbc-455">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-455">The following table lists the available log levels.</span></span> <span data-ttu-id="93fbc-456">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="93fbc-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="93fbc-457">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="93fbc-458">String</span><span class="sxs-lookup"><span data-stu-id="93fbc-458">String</span></span>                      | <span data-ttu-id="93fbc-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="93fbc-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="93fbc-460">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="93fbc-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="93fbc-461">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="93fbc-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="93fbc-462">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="93fbc-463">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="93fbc-464">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="93fbc-465">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="93fbc-466">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="93fbc-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="93fbc-467">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="93fbc-468">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="93fbc-469">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-469">Configure allowed transports</span></span>

<span data-ttu-id="93fbc-470">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="93fbc-471">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="93fbc-472">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-472">All transports are enabled by default.</span></span>

<span data-ttu-id="93fbc-473">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="93fbc-474">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="93fbc-475">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="93fbc-476">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="93fbc-477">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="93fbc-478">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="93fbc-479">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-479">Configure bearer authentication</span></span>

<span data-ttu-id="93fbc-480">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="93fbc-481">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="93fbc-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="93fbc-482">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="93fbc-483">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="93fbc-484">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="93fbc-485">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="93fbc-486">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="93fbc-487">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="93fbc-488">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="93fbc-489">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="93fbc-490">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-491">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-492">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-492">Option</span></span> | <span data-ttu-id="93fbc-493">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-493">Default value</span></span> | <span data-ttu-id="93fbc-494">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="93fbc-495">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-496">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-496">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-497">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-498">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-499">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="93fbc-500">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-500">15 seconds</span></span> | <span data-ttu-id="93fbc-501">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-502">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-503">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-504">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-505">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-505">15 seconds</span></span> | <span data-ttu-id="93fbc-506">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-507">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-508">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="93fbc-509">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="93fbc-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-511">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-511">Option</span></span> | <span data-ttu-id="93fbc-512">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-512">Default value</span></span> | <span data-ttu-id="93fbc-513">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="93fbc-514">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-515">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-515">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-516">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="93fbc-517">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-518">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="93fbc-519">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-520">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-521">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-522">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-523">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-523">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-524">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-524">Option</span></span> | <span data-ttu-id="93fbc-525">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-525">Default value</span></span> | <span data-ttu-id="93fbc-526">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="93fbc-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="93fbc-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="93fbc-528">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-529">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-529">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-530">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-531">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-532">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="93fbc-533">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-533">15 seconds</span></span> | <span data-ttu-id="93fbc-534">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-535">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-536">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-537">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="93fbc-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="93fbc-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="93fbc-539">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-540">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-541">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-542">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="93fbc-543">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-543">Configure additional options</span></span>

<span data-ttu-id="93fbc-544">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-545">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-546">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-546">.NET Option</span></span> |  <span data-ttu-id="93fbc-547">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-547">Default value</span></span> | <span data-ttu-id="93fbc-548">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="93fbc-549">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="93fbc-550">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-551">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-552">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="93fbc-553">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-553">Empty</span></span> | <span data-ttu-id="93fbc-554">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="93fbc-555">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-555">Empty</span></span> | <span data-ttu-id="93fbc-556">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="93fbc-557">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-557">Empty</span></span> | <span data-ttu-id="93fbc-558">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="93fbc-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="93fbc-559">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-559">5 seconds</span></span> | <span data-ttu-id="93fbc-560">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-560">WebSockets only.</span></span> <span data-ttu-id="93fbc-561">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="93fbc-562">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="93fbc-563">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-563">Empty</span></span> | <span data-ttu-id="93fbc-564">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="93fbc-565">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="93fbc-566">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="93fbc-567">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="93fbc-568">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="93fbc-569">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="93fbc-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="93fbc-570">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="93fbc-571">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="93fbc-572">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="93fbc-573">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="93fbc-574">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="93fbc-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-576">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-576">JavaScript Option</span></span> | <span data-ttu-id="93fbc-577">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-577">Default Value</span></span> | <span data-ttu-id="93fbc-578">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="93fbc-579">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="93fbc-580">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="93fbc-581">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-582">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-583">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-584">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-584">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-585">Java オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-585">Java Option</span></span> | <span data-ttu-id="93fbc-586">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-586">Default Value</span></span> | <span data-ttu-id="93fbc-587">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="93fbc-588">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="93fbc-589">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-590">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-591">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="93fbc-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="93fbc-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="93fbc-593">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-593">Empty</span></span> | <span data-ttu-id="93fbc-594">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="93fbc-595">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="93fbc-596">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="93fbc-597">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="93fbc-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="93fbc-598">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="93fbc-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="93fbc-599">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-600">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="93fbc-601">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="93fbc-602">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="93fbc-603">`AddJsonProtocol`は、の[ SignalR Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="93fbc-604">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="93fbc-605">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="93fbc-606">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="93fbc-607">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="93fbc-608">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="93fbc-609">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="93fbc-610">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="93fbc-611">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="93fbc-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="93fbc-612">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="93fbc-613">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-613">MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-614">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="93fbc-615">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-616">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="93fbc-617">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-617">Configure server options</span></span>

<span data-ttu-id="93fbc-618">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="93fbc-619">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-619">Option</span></span> | <span data-ttu-id="93fbc-620">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-620">Default Value</span></span> | <span data-ttu-id="93fbc-621">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="93fbc-622">30 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-622">30 seconds</span></span> | <span data-ttu-id="93fbc-623">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="93fbc-624">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="93fbc-625">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="93fbc-626">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-626">15 seconds</span></span> | <span data-ttu-id="93fbc-627">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="93fbc-628">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-629">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-630">15 seconds</span></span> | <span data-ttu-id="93fbc-631">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="93fbc-632">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="93fbc-633">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="93fbc-634">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="93fbc-634">All installed protocols</span></span> | <span data-ttu-id="93fbc-635">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="93fbc-635">Protocols supported by this hub.</span></span> <span data-ttu-id="93fbc-636">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="93fbc-637">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="93fbc-638">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="93fbc-639">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="93fbc-640">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="93fbc-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-641">32 KB</span></span> | <span data-ttu-id="93fbc-642">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="93fbc-643">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="93fbc-644">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="93fbc-645">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="93fbc-646">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="93fbc-647">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="93fbc-648">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="93fbc-649">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-649">Option</span></span> | <span data-ttu-id="93fbc-650">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-650">Default Value</span></span> | <span data-ttu-id="93fbc-651">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="93fbc-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-652">32 KB</span></span> | <span data-ttu-id="93fbc-653">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="93fbc-654">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="93fbc-655">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="93fbc-656">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="93fbc-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="93fbc-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-657">32 KB</span></span> | <span data-ttu-id="93fbc-658">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="93fbc-659">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="93fbc-660">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-660">All Transports are enabled.</span></span> | <span data-ttu-id="93fbc-661">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="93fbc-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="93fbc-662">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-662">See below.</span></span> | <span data-ttu-id="93fbc-663">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="93fbc-664">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-664">See below.</span></span> | <span data-ttu-id="93fbc-665">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="93fbc-666">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="93fbc-667">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-667">Option</span></span> | <span data-ttu-id="93fbc-668">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-668">Default Value</span></span> | <span data-ttu-id="93fbc-669">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="93fbc-670">90秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-670">90 seconds</span></span> | <span data-ttu-id="93fbc-671">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="93fbc-672">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="93fbc-673">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="93fbc-674">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-674">Option</span></span> | <span data-ttu-id="93fbc-675">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-675">Default Value</span></span> | <span data-ttu-id="93fbc-676">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="93fbc-677">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-677">5 seconds</span></span> | <span data-ttu-id="93fbc-678">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="93fbc-679">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="93fbc-680">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="93fbc-681">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-681">Configure client options</span></span>

<span data-ttu-id="93fbc-682">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="93fbc-683">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="93fbc-684">ログの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-684">Configure logging</span></span>

<span data-ttu-id="93fbc-685">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="93fbc-686">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="93fbc-687">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-688">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="93fbc-689">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="93fbc-690">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="93fbc-691">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="93fbc-692">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="93fbc-693">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="93fbc-694">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="93fbc-695">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="93fbc-696">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="93fbc-697">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-697">The following table lists the available log levels.</span></span> <span data-ttu-id="93fbc-698">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="93fbc-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="93fbc-699">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="93fbc-700">String</span><span class="sxs-lookup"><span data-stu-id="93fbc-700">String</span></span>                      | <span data-ttu-id="93fbc-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="93fbc-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="93fbc-702">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="93fbc-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="93fbc-703">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="93fbc-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="93fbc-704">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="93fbc-705">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="93fbc-706">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="93fbc-707">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="93fbc-708">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="93fbc-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="93fbc-709">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="93fbc-710">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="93fbc-711">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-711">Configure allowed transports</span></span>

<span data-ttu-id="93fbc-712">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="93fbc-713">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="93fbc-714">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-714">All transports are enabled by default.</span></span>

<span data-ttu-id="93fbc-715">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="93fbc-716">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="93fbc-717">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="93fbc-718">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="93fbc-719">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="93fbc-720">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="93fbc-721">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-721">Configure bearer authentication</span></span>

<span data-ttu-id="93fbc-722">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="93fbc-723">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="93fbc-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="93fbc-724">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="93fbc-725">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="93fbc-726">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="93fbc-727">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="93fbc-728">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="93fbc-729">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="93fbc-730">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="93fbc-731">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="93fbc-732">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-733">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-734">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-734">Option</span></span> | <span data-ttu-id="93fbc-735">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-735">Default value</span></span> | <span data-ttu-id="93fbc-736">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="93fbc-737">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-738">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-738">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-739">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-740">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-741">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="93fbc-742">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-742">15 seconds</span></span> | <span data-ttu-id="93fbc-743">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-744">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-745">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-746">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-747">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-747">15 seconds</span></span> | <span data-ttu-id="93fbc-748">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-749">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-750">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="93fbc-751">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="93fbc-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-753">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-753">Option</span></span> | <span data-ttu-id="93fbc-754">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-754">Default value</span></span> | <span data-ttu-id="93fbc-755">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="93fbc-756">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-757">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-757">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-758">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="93fbc-759">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-760">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="93fbc-761">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-762">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-763">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-764">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-765">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-765">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-766">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-766">Option</span></span> | <span data-ttu-id="93fbc-767">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-767">Default value</span></span> | <span data-ttu-id="93fbc-768">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="93fbc-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="93fbc-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="93fbc-770">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-771">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-771">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-772">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-773">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-774">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="93fbc-775">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-775">15 seconds</span></span> | <span data-ttu-id="93fbc-776">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-777">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-778">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-779">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="93fbc-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="93fbc-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="93fbc-781">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-782">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-783">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-784">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="93fbc-785">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-785">Configure additional options</span></span>

<span data-ttu-id="93fbc-786">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-787">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-788">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-788">.NET Option</span></span> |  <span data-ttu-id="93fbc-789">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-789">Default value</span></span> | <span data-ttu-id="93fbc-790">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="93fbc-791">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="93fbc-792">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-793">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-794">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="93fbc-795">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-795">Empty</span></span> | <span data-ttu-id="93fbc-796">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="93fbc-797">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-797">Empty</span></span> | <span data-ttu-id="93fbc-798">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="93fbc-799">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-799">Empty</span></span> | <span data-ttu-id="93fbc-800">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="93fbc-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="93fbc-801">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-801">5 seconds</span></span> | <span data-ttu-id="93fbc-802">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-802">WebSockets only.</span></span> <span data-ttu-id="93fbc-803">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="93fbc-804">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="93fbc-805">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-805">Empty</span></span> | <span data-ttu-id="93fbc-806">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="93fbc-807">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="93fbc-808">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="93fbc-809">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="93fbc-810">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="93fbc-811">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="93fbc-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="93fbc-812">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="93fbc-813">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="93fbc-814">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="93fbc-815">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="93fbc-816">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="93fbc-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-818">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-818">JavaScript Option</span></span> | <span data-ttu-id="93fbc-819">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-819">Default Value</span></span> | <span data-ttu-id="93fbc-820">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="93fbc-821">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="93fbc-822">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="93fbc-823">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-824">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-825">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-826">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-826">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-827">Java オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-827">Java Option</span></span> | <span data-ttu-id="93fbc-828">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-828">Default Value</span></span> | <span data-ttu-id="93fbc-829">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="93fbc-830">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="93fbc-831">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-832">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-833">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="93fbc-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="93fbc-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="93fbc-835">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-835">Empty</span></span> | <span data-ttu-id="93fbc-836">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="93fbc-837">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="93fbc-838">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="93fbc-839">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="93fbc-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="93fbc-840">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="93fbc-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="93fbc-841">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-842">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="93fbc-843">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="93fbc-844">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドに[Add を SignalR 追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)した後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="93fbc-845">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="93fbc-846">そのオブジェクトの [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="93fbc-847">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="93fbc-848">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="93fbc-849">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="93fbc-850">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="93fbc-851">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="93fbc-852">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-852">MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-853">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="93fbc-854">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-855">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="93fbc-856">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-856">Configure server options</span></span>

<span data-ttu-id="93fbc-857">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="93fbc-858">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-858">Option</span></span> | <span data-ttu-id="93fbc-859">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-859">Default Value</span></span> | <span data-ttu-id="93fbc-860">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="93fbc-861">30 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-861">30 seconds</span></span> | <span data-ttu-id="93fbc-862">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="93fbc-863">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="93fbc-864">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="93fbc-865">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-865">15 seconds</span></span> | <span data-ttu-id="93fbc-866">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="93fbc-867">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-868">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-869">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-869">15 seconds</span></span> | <span data-ttu-id="93fbc-870">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="93fbc-871">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="93fbc-872">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="93fbc-873">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="93fbc-873">All installed protocols</span></span> | <span data-ttu-id="93fbc-874">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="93fbc-874">Protocols supported by this hub.</span></span> <span data-ttu-id="93fbc-875">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="93fbc-876">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="93fbc-877">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="93fbc-878">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="93fbc-879">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="93fbc-880">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="93fbc-881">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="93fbc-882">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="93fbc-883">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="93fbc-884">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-884">Option</span></span> | <span data-ttu-id="93fbc-885">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-885">Default Value</span></span> | <span data-ttu-id="93fbc-886">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="93fbc-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-887">32 KB</span></span> | <span data-ttu-id="93fbc-888">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="93fbc-889">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="93fbc-890">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="93fbc-891">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="93fbc-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="93fbc-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-892">32 KB</span></span> | <span data-ttu-id="93fbc-893">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="93fbc-894">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="93fbc-895">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-895">All Transports are enabled.</span></span> | <span data-ttu-id="93fbc-896">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="93fbc-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="93fbc-897">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-897">See below.</span></span> | <span data-ttu-id="93fbc-898">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="93fbc-899">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-899">See below.</span></span> | <span data-ttu-id="93fbc-900">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="93fbc-901">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="93fbc-902">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-902">Option</span></span> | <span data-ttu-id="93fbc-903">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-903">Default Value</span></span> | <span data-ttu-id="93fbc-904">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="93fbc-905">90秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-905">90 seconds</span></span> | <span data-ttu-id="93fbc-906">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="93fbc-907">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="93fbc-908">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="93fbc-909">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-909">Option</span></span> | <span data-ttu-id="93fbc-910">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-910">Default Value</span></span> | <span data-ttu-id="93fbc-911">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="93fbc-912">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-912">5 seconds</span></span> | <span data-ttu-id="93fbc-913">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="93fbc-914">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="93fbc-915">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="93fbc-916">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-916">Configure client options</span></span>

<span data-ttu-id="93fbc-917">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="93fbc-918">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="93fbc-919">ログの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-919">Configure logging</span></span>

<span data-ttu-id="93fbc-920">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="93fbc-921">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="93fbc-922">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-923">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="93fbc-924">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="93fbc-925">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="93fbc-926">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="93fbc-927">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="93fbc-928">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="93fbc-929">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="93fbc-930">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="93fbc-931">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="93fbc-932">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="93fbc-933">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="93fbc-934">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="93fbc-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="93fbc-935">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="93fbc-936">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="93fbc-937">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-937">Configure allowed transports</span></span>

<span data-ttu-id="93fbc-938">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="93fbc-939">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="93fbc-940">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-940">All transports are enabled by default.</span></span>

<span data-ttu-id="93fbc-941">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="93fbc-942">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="93fbc-943">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="93fbc-944">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-944">Configure bearer authentication</span></span>

<span data-ttu-id="93fbc-945">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="93fbc-946">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="93fbc-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="93fbc-947">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="93fbc-948">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="93fbc-949">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="93fbc-950">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="93fbc-951">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="93fbc-952">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="93fbc-953">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="93fbc-954">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="93fbc-955">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-956">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-957">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-957">Option</span></span> | <span data-ttu-id="93fbc-958">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-958">Default value</span></span> | <span data-ttu-id="93fbc-959">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="93fbc-960">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-961">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-961">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-962">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-963">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-964">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="93fbc-965">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-965">15 seconds</span></span> | <span data-ttu-id="93fbc-966">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-967">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-968">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-969">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-970">15 seconds</span></span> | <span data-ttu-id="93fbc-971">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-972">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-973">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="93fbc-974">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="93fbc-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-976">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-976">Option</span></span> | <span data-ttu-id="93fbc-977">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-977">Default value</span></span> | <span data-ttu-id="93fbc-978">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="93fbc-979">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-980">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-980">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-981">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="93fbc-982">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-983">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="93fbc-984">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-985">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-986">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-987">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-988">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-988">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-989">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-989">Option</span></span> | <span data-ttu-id="93fbc-990">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-990">Default value</span></span> | <span data-ttu-id="93fbc-991">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="93fbc-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="93fbc-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="93fbc-993">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-994">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-994">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-995">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-996">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-997">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="93fbc-998">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-998">15 seconds</span></span> | <span data-ttu-id="93fbc-999">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-1000">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-1001">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-1002">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="93fbc-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="93fbc-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="93fbc-1004">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-1005">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="93fbc-1006">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="93fbc-1007">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="93fbc-1008">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-1008">Configure additional options</span></span>

<span data-ttu-id="93fbc-1009">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-1011">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1011">.NET Option</span></span> |  <span data-ttu-id="93fbc-1012">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1012">Default value</span></span> | <span data-ttu-id="93fbc-1013">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="93fbc-1014">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="93fbc-1015">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-1016">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-1017">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="93fbc-1018">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1018">Empty</span></span> | <span data-ttu-id="93fbc-1019">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="93fbc-1020">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1020">Empty</span></span> | <span data-ttu-id="93fbc-1021">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="93fbc-1022">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1022">Empty</span></span> | <span data-ttu-id="93fbc-1023">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="93fbc-1024">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-1024">5 seconds</span></span> | <span data-ttu-id="93fbc-1025">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1025">WebSockets only.</span></span> <span data-ttu-id="93fbc-1026">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="93fbc-1027">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="93fbc-1028">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1028">Empty</span></span> | <span data-ttu-id="93fbc-1029">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="93fbc-1030">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="93fbc-1031">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="93fbc-1032">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="93fbc-1033">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="93fbc-1034">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="93fbc-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="93fbc-1035">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="93fbc-1036">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="93fbc-1037">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="93fbc-1038">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="93fbc-1039">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="93fbc-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-1041">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1041">JavaScript Option</span></span> | <span data-ttu-id="93fbc-1042">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1042">Default Value</span></span> | <span data-ttu-id="93fbc-1043">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="93fbc-1044">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="93fbc-1045">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="93fbc-1046">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-1047">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-1048">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-1049">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-1050">Java オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1050">Java Option</span></span> | <span data-ttu-id="93fbc-1051">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1051">Default Value</span></span> | <span data-ttu-id="93fbc-1052">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="93fbc-1053">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="93fbc-1054">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-1055">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-1056">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="93fbc-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="93fbc-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="93fbc-1058">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1058">Empty</span></span> | <span data-ttu-id="93fbc-1059">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="93fbc-1060">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="93fbc-1061">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="93fbc-1062">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="93fbc-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="93fbc-1063">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="93fbc-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="93fbc-1064">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-1065">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="93fbc-1066">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="93fbc-1067">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドに[Add を SignalR 追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)した後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="93fbc-1068">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="93fbc-1069">そのオブジェクトの [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="93fbc-1070">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="93fbc-1071">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="93fbc-1072">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="93fbc-1073">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="93fbc-1074">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="93fbc-1075">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1075">MessagePack serialization options</span></span>

<span data-ttu-id="93fbc-1076">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="93fbc-1077">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-1078">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="93fbc-1079">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-1079">Configure server options</span></span>

<span data-ttu-id="93fbc-1080">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="93fbc-1081">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1081">Option</span></span> | <span data-ttu-id="93fbc-1082">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1082">Default Value</span></span> | <span data-ttu-id="93fbc-1083">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="93fbc-1084">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-1084">15 seconds</span></span> | <span data-ttu-id="93fbc-1085">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="93fbc-1086">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-1087">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="93fbc-1088">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-1088">15 seconds</span></span> | <span data-ttu-id="93fbc-1089">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="93fbc-1090">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="93fbc-1091">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="93fbc-1092">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="93fbc-1092">All installed protocols</span></span> | <span data-ttu-id="93fbc-1093">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="93fbc-1094">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="93fbc-1095">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="93fbc-1096">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="93fbc-1097">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="93fbc-1098">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="93fbc-1099">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="93fbc-1100">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="93fbc-1101">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="93fbc-1102">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="93fbc-1103">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1103">Option</span></span> | <span data-ttu-id="93fbc-1104">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1104">Default Value</span></span> | <span data-ttu-id="93fbc-1105">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="93fbc-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-1106">32 KB</span></span> | <span data-ttu-id="93fbc-1107">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="93fbc-1108">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="93fbc-1109">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="93fbc-1110">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="93fbc-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="93fbc-1111">32 KB</span></span> | <span data-ttu-id="93fbc-1112">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="93fbc-1113">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="93fbc-1114">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1114">All Transports are enabled.</span></span> | <span data-ttu-id="93fbc-1115">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="93fbc-1116">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1116">See below.</span></span> | <span data-ttu-id="93fbc-1117">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="93fbc-1118">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1118">See below.</span></span> | <span data-ttu-id="93fbc-1119">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="93fbc-1120">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="93fbc-1121">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1121">Option</span></span> | <span data-ttu-id="93fbc-1122">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1122">Default Value</span></span> | <span data-ttu-id="93fbc-1123">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="93fbc-1124">90秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-1124">90 seconds</span></span> | <span data-ttu-id="93fbc-1125">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="93fbc-1126">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="93fbc-1127">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="93fbc-1128">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1128">Option</span></span> | <span data-ttu-id="93fbc-1129">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1129">Default Value</span></span> | <span data-ttu-id="93fbc-1130">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="93fbc-1131">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-1131">5 seconds</span></span> | <span data-ttu-id="93fbc-1132">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="93fbc-1133">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="93fbc-1134">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="93fbc-1135">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-1135">Configure client options</span></span>

<span data-ttu-id="93fbc-1136">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="93fbc-1137">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="93fbc-1138">ログの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-1138">Configure logging</span></span>

<span data-ttu-id="93fbc-1139">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="93fbc-1140">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="93fbc-1141">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="93fbc-1142">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="93fbc-1143">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="93fbc-1144">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="93fbc-1145">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="93fbc-1146">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="93fbc-1147">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="93fbc-1148">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="93fbc-1149">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="93fbc-1150">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="93fbc-1151">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="93fbc-1152">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="93fbc-1153">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="93fbc-1154">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="93fbc-1155">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="93fbc-1156">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-1156">Configure allowed transports</span></span>

<span data-ttu-id="93fbc-1157">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="93fbc-1158">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="93fbc-1159">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="93fbc-1160">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="93fbc-1161">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="93fbc-1162">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-1162">Configure bearer authentication</span></span>

<span data-ttu-id="93fbc-1163">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="93fbc-1164">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="93fbc-1165">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は **例外** です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="93fbc-1166">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="93fbc-1167">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="93fbc-1168">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="93fbc-1169">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="93fbc-1170">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="93fbc-1171">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="93fbc-1172">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="93fbc-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="93fbc-1173">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-1175">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1175">Option</span></span> | <span data-ttu-id="93fbc-1176">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1176">Default value</span></span> | <span data-ttu-id="93fbc-1177">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="93fbc-1178">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-1179">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1179">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-1180">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-1181">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-1182">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="93fbc-1183">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-1183">15 seconds</span></span> | <span data-ttu-id="93fbc-1184">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-1185">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="93fbc-1186">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-1187">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="93fbc-1188">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="93fbc-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-1190">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1190">Option</span></span> | <span data-ttu-id="93fbc-1191">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1191">Default value</span></span> | <span data-ttu-id="93fbc-1192">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="93fbc-1193">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-1194">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1194">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-1195">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="93fbc-1196">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-1197">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-1198">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-1199">オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1199">Option</span></span> | <span data-ttu-id="93fbc-1200">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1200">Default value</span></span> | <span data-ttu-id="93fbc-1201">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="93fbc-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="93fbc-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="93fbc-1203">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="93fbc-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="93fbc-1204">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1204">Timeout for server activity.</span></span> <span data-ttu-id="93fbc-1205">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-1206">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="93fbc-1207">推奨値は、サーバーの値の少なくとも2倍の数で、 `KeepAliveInterval` ping が到着するまでの時間を確保します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="93fbc-1208">15 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-1208">15 seconds</span></span> | <span data-ttu-id="93fbc-1209">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="93fbc-1210">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="93fbc-1211">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="93fbc-1212">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="93fbc-1213">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="93fbc-1213">Configure additional options</span></span>

<span data-ttu-id="93fbc-1214">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="93fbc-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="93fbc-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="93fbc-1216">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1216">.NET Option</span></span> |  <span data-ttu-id="93fbc-1217">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1217">Default value</span></span> | <span data-ttu-id="93fbc-1218">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="93fbc-1219">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="93fbc-1220">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-1221">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-1222">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="93fbc-1223">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1223">Empty</span></span> | <span data-ttu-id="93fbc-1224">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="93fbc-1225">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1225">Empty</span></span> | <span data-ttu-id="93fbc-1226">cookieすべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="93fbc-1227">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1227">Empty</span></span> | <span data-ttu-id="93fbc-1228">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="93fbc-1229">5 秒</span><span class="sxs-lookup"><span data-stu-id="93fbc-1229">5 seconds</span></span> | <span data-ttu-id="93fbc-1230">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1230">WebSockets only.</span></span> <span data-ttu-id="93fbc-1231">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="93fbc-1232">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="93fbc-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1233">Empty</span></span> | <span data-ttu-id="93fbc-1234">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="93fbc-1235">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="93fbc-1236">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="93fbc-1237">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="93fbc-1238">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="93fbc-1239">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( Cookie s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="93fbc-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="93fbc-1240">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="93fbc-1241">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="93fbc-1242">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="93fbc-1243">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="93fbc-1244">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="93fbc-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="93fbc-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="93fbc-1246">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1246">JavaScript Option</span></span> | <span data-ttu-id="93fbc-1247">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1247">Default Value</span></span> | <span data-ttu-id="93fbc-1248">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="93fbc-1249">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="93fbc-1250">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="93fbc-1251">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-1252">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-1253">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="93fbc-1254">Java</span><span class="sxs-lookup"><span data-stu-id="93fbc-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="93fbc-1255">Java オプション</span><span class="sxs-lookup"><span data-stu-id="93fbc-1255">Java Option</span></span> | <span data-ttu-id="93fbc-1256">既定値</span><span class="sxs-lookup"><span data-stu-id="93fbc-1256">Default Value</span></span> | <span data-ttu-id="93fbc-1257">説明</span><span class="sxs-lookup"><span data-stu-id="93fbc-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="93fbc-1258">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="93fbc-1259">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="93fbc-1260">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="93fbc-1261">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="93fbc-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="93fbc-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="93fbc-1263">Empty</span><span class="sxs-lookup"><span data-stu-id="93fbc-1263">Empty</span></span> | <span data-ttu-id="93fbc-1264">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="93fbc-1265">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="93fbc-1266">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="93fbc-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="93fbc-1267">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="93fbc-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="93fbc-1268">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="93fbc-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
