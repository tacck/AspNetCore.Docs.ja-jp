---
title: ASP.NET Core SignalR の構成
author: bradygaster
description: ASP.NET Core アプリを構成する方法について説明 SignalR します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c711c2163908e3fdd20e3bb497f333ebd495d921
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406837"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="2f7c1-103">ASP.NET Core SignalR の構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2f7c1-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-105">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2f7c1-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2f7c1-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2f7c1-108">`AddJsonProtocol`は、 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f7c1-109">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2f7c1-110">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2f7c1-111">詳細については、[ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2f7c1-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="2f7c1-113">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2f7c1-114">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2f7c1-115">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2f7c1-116">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="2f7c1-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2f7c1-117">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2f7c1-118">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-118">MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-119">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2f7c1-120">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-121">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2f7c1-122">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-122">Configure server options</span></span>

<span data-ttu-id="2f7c1-123">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2f7c1-124">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-124">Option</span></span> | <span data-ttu-id="2f7c1-125">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-125">Default Value</span></span> | <span data-ttu-id="2f7c1-126">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2f7c1-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-127">30 seconds</span></span> | <span data-ttu-id="2f7c1-128">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2f7c1-129">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2f7c1-130">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-131">15 seconds</span></span> | <span data-ttu-id="2f7c1-132">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2f7c1-133">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-134">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-135">15 seconds</span></span> | <span data-ttu-id="2f7c1-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2f7c1-137">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2f7c1-138">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2f7c1-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="2f7c1-139">All installed protocols</span></span> | <span data-ttu-id="2f7c1-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-140">Protocols supported by this hub.</span></span> <span data-ttu-id="2f7c1-141">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2f7c1-142">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2f7c1-143">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2f7c1-144">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2f7c1-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2f7c1-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-146">32 KB</span></span> | <span data-ttu-id="2f7c1-147">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2f7c1-148">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2f7c1-149">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2f7c1-150">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2f7c1-151">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2f7c1-152">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2f7c1-153">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2f7c1-154">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-154">Option</span></span> | <span data-ttu-id="2f7c1-155">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-155">Default Value</span></span> | <span data-ttu-id="2f7c1-156">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2f7c1-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-157">32 KB</span></span> | <span data-ttu-id="2f7c1-158">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2f7c1-159">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2f7c1-160">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2f7c1-161">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2f7c1-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-162">32 KB</span></span> | <span data-ttu-id="2f7c1-163">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2f7c1-164">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2f7c1-165">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-165">All Transports are enabled.</span></span> | <span data-ttu-id="2f7c1-166">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2f7c1-167">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-167">See below.</span></span> | <span data-ttu-id="2f7c1-168">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2f7c1-169">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-169">See below.</span></span> | <span data-ttu-id="2f7c1-170">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="2f7c1-171">0</span><span class="sxs-lookup"><span data-stu-id="2f7c1-171">0</span></span> | <span data-ttu-id="2f7c1-172">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="2f7c1-173">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="2f7c1-174">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2f7c1-175">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-175">Option</span></span> | <span data-ttu-id="2f7c1-176">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-176">Default Value</span></span> | <span data-ttu-id="2f7c1-177">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2f7c1-178">90秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-178">90 seconds</span></span> | <span data-ttu-id="2f7c1-179">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2f7c1-180">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2f7c1-181">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2f7c1-182">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-182">Option</span></span> | <span data-ttu-id="2f7c1-183">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-183">Default Value</span></span> | <span data-ttu-id="2f7c1-184">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2f7c1-185">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-185">5 seconds</span></span> | <span data-ttu-id="2f7c1-186">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2f7c1-187">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2f7c1-188">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2f7c1-189">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-189">Configure client options</span></span>

<span data-ttu-id="2f7c1-190">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2f7c1-191">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2f7c1-192">ログの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-192">Configure logging</span></span>

<span data-ttu-id="2f7c1-193">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2f7c1-194">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2f7c1-195">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-196">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2f7c1-197">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2f7c1-198">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2f7c1-199">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2f7c1-200">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2f7c1-201">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2f7c1-202">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2f7c1-203">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2f7c1-204">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2f7c1-205">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-205">The following table lists the available log levels.</span></span> <span data-ttu-id="2f7c1-206">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2f7c1-207">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2f7c1-208">String</span><span class="sxs-lookup"><span data-stu-id="2f7c1-208">String</span></span>                      | <span data-ttu-id="2f7c1-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2f7c1-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2f7c1-210">`info` **または** `information`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2f7c1-211">`warn` **または** `warning`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2f7c1-212">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2f7c1-213">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2f7c1-214">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2f7c1-215">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2f7c1-216">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2f7c1-217">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2f7c1-218">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2f7c1-219">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-219">Configure allowed transports</span></span>

<span data-ttu-id="2f7c1-220">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2f7c1-221">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2f7c1-222">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-222">All transports are enabled by default.</span></span>

<span data-ttu-id="2f7c1-223">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2f7c1-224">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2f7c1-225">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2f7c1-226">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2f7c1-227">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2f7c1-228">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2f7c1-229">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-229">Configure bearer authentication</span></span>

<span data-ttu-id="2f7c1-230">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2f7c1-231">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2f7c1-232">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2f7c1-233">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2f7c1-234">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2f7c1-235">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2f7c1-236">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2f7c1-237">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2f7c1-238">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2f7c1-239">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2f7c1-240">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-241">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-242">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-242">Option</span></span> | <span data-ttu-id="2f7c1-243">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-243">Default value</span></span> | <span data-ttu-id="2f7c1-244">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2f7c1-245">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-246">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-246">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-247">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-248">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-249">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-250">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-250">15 seconds</span></span> | <span data-ttu-id="2f7c1-251">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-252">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-253">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-254">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-255">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-255">15 seconds</span></span> | <span data-ttu-id="2f7c1-256">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-257">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-258">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2f7c1-259">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-261">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-261">Option</span></span> | <span data-ttu-id="2f7c1-262">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-262">Default value</span></span> | <span data-ttu-id="2f7c1-263">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2f7c1-264">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-265">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-265">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-266">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2f7c1-267">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-268">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2f7c1-269">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-270">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-271">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-272">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-273">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-273">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-274">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-274">Option</span></span> | <span data-ttu-id="2f7c1-275">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-275">Default value</span></span> | <span data-ttu-id="2f7c1-276">説明</span><span class="sxs-lookup"><span data-stu-id="2f7c1-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2f7c1-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2f7c1-278">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-279">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-279">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-280">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-281">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-282">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2f7c1-283">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-283">15 seconds</span></span> | <span data-ttu-id="2f7c1-284">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-285">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-286">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-287">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2f7c1-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2f7c1-289">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-290">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-291">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-292">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2f7c1-293">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-293">Configure additional options</span></span>

<span data-ttu-id="2f7c1-294">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-295">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-296">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-296">.NET Option</span></span> |  <span data-ttu-id="2f7c1-297">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-297">Default value</span></span> | <span data-ttu-id="2f7c1-298">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-299">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2f7c1-300">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-301">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-302">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2f7c1-303">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-303">Empty</span></span> | <span data-ttu-id="2f7c1-304">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2f7c1-305">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-305">Empty</span></span> | <span data-ttu-id="2f7c1-306">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2f7c1-307">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-307">Empty</span></span> | <span data-ttu-id="2f7c1-308">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2f7c1-309">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-309">5 seconds</span></span> | <span data-ttu-id="2f7c1-310">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-310">WebSockets only.</span></span> <span data-ttu-id="2f7c1-311">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2f7c1-312">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2f7c1-313">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-313">Empty</span></span> | <span data-ttu-id="2f7c1-314">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2f7c1-315">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2f7c1-316">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="2f7c1-317">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2f7c1-318">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2f7c1-319">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="2f7c1-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2f7c1-320">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2f7c1-321">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2f7c1-322">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2f7c1-323">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2f7c1-324">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-326">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-326">JavaScript Option</span></span> | <span data-ttu-id="2f7c1-327">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-327">Default Value</span></span> | <span data-ttu-id="2f7c1-328">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2f7c1-329">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="2f7c1-330">すべての HTTP 要求と共に送信されるヘッダーのディクショナリ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="2f7c1-331">ブラウザーでヘッダーを送信することは、Websocket やストリームでは機能しません <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2f7c1-332">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2f7c1-333">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-334">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-335">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="2f7c1-336">CORS 要求と共に資格情報を送信するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="2f7c1-337">Azure App Service は固定セッションに cookie を使用します。このオプションを正しく動作させるには、このオプションを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="2f7c1-338">での CORS の詳細につい SignalR ては、「」を参照してください <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-339">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-339">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-340">Java オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-340">Java Option</span></span> | <span data-ttu-id="2f7c1-341">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-341">Default Value</span></span> | <span data-ttu-id="2f7c1-342">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-343">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2f7c1-344">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-345">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-346">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2f7c1-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2f7c1-348">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-348">Empty</span></span> | <span data-ttu-id="2f7c1-349">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2f7c1-350">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2f7c1-351">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2f7c1-352">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2f7c1-353">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2f7c1-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2f7c1-354">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-355">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2f7c1-356">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2f7c1-357">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2f7c1-358">`AddJsonProtocol`は、 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f7c1-359">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2f7c1-360">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2f7c1-361">詳細については、[ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2f7c1-362">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="2f7c1-363">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2f7c1-364">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2f7c1-365">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2f7c1-366">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="2f7c1-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2f7c1-367">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2f7c1-368">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-368">MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-369">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2f7c1-370">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-371">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2f7c1-372">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-372">Configure server options</span></span>

<span data-ttu-id="2f7c1-373">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2f7c1-374">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-374">Option</span></span> | <span data-ttu-id="2f7c1-375">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-375">Default Value</span></span> | <span data-ttu-id="2f7c1-376">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2f7c1-377">30 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-377">30 seconds</span></span> | <span data-ttu-id="2f7c1-378">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2f7c1-379">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2f7c1-380">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-381">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-381">15 seconds</span></span> | <span data-ttu-id="2f7c1-382">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2f7c1-383">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-384">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-385">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-385">15 seconds</span></span> | <span data-ttu-id="2f7c1-386">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2f7c1-387">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2f7c1-388">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2f7c1-389">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="2f7c1-389">All installed protocols</span></span> | <span data-ttu-id="2f7c1-390">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-390">Protocols supported by this hub.</span></span> <span data-ttu-id="2f7c1-391">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2f7c1-392">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2f7c1-393">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2f7c1-394">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2f7c1-395">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2f7c1-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-396">32 KB</span></span> | <span data-ttu-id="2f7c1-397">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2f7c1-398">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2f7c1-399">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2f7c1-400">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2f7c1-401">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2f7c1-402">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2f7c1-403">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2f7c1-404">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-404">Option</span></span> | <span data-ttu-id="2f7c1-405">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-405">Default Value</span></span> | <span data-ttu-id="2f7c1-406">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2f7c1-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-407">32 KB</span></span> | <span data-ttu-id="2f7c1-408">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2f7c1-409">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2f7c1-410">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2f7c1-411">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2f7c1-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-412">32 KB</span></span> | <span data-ttu-id="2f7c1-413">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2f7c1-414">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2f7c1-415">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-415">All Transports are enabled.</span></span> | <span data-ttu-id="2f7c1-416">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2f7c1-417">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-417">See below.</span></span> | <span data-ttu-id="2f7c1-418">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2f7c1-419">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-419">See below.</span></span> | <span data-ttu-id="2f7c1-420">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="2f7c1-421">0</span><span class="sxs-lookup"><span data-stu-id="2f7c1-421">0</span></span> | <span data-ttu-id="2f7c1-422">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="2f7c1-423">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="2f7c1-424">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2f7c1-425">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-425">Option</span></span> | <span data-ttu-id="2f7c1-426">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-426">Default Value</span></span> | <span data-ttu-id="2f7c1-427">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2f7c1-428">90秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-428">90 seconds</span></span> | <span data-ttu-id="2f7c1-429">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2f7c1-430">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2f7c1-431">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2f7c1-432">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-432">Option</span></span> | <span data-ttu-id="2f7c1-433">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-433">Default Value</span></span> | <span data-ttu-id="2f7c1-434">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2f7c1-435">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-435">5 seconds</span></span> | <span data-ttu-id="2f7c1-436">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2f7c1-437">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2f7c1-438">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2f7c1-439">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-439">Configure client options</span></span>

<span data-ttu-id="2f7c1-440">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2f7c1-441">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2f7c1-442">ログの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-442">Configure logging</span></span>

<span data-ttu-id="2f7c1-443">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2f7c1-444">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2f7c1-445">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-446">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2f7c1-447">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2f7c1-448">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2f7c1-449">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2f7c1-450">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2f7c1-451">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2f7c1-452">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2f7c1-453">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2f7c1-454">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2f7c1-455">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-455">The following table lists the available log levels.</span></span> <span data-ttu-id="2f7c1-456">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2f7c1-457">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2f7c1-458">String</span><span class="sxs-lookup"><span data-stu-id="2f7c1-458">String</span></span>                      | <span data-ttu-id="2f7c1-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2f7c1-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2f7c1-460">`info` **または** `information`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2f7c1-461">`warn` **または** `warning`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2f7c1-462">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2f7c1-463">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2f7c1-464">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2f7c1-465">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2f7c1-466">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2f7c1-467">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2f7c1-468">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2f7c1-469">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-469">Configure allowed transports</span></span>

<span data-ttu-id="2f7c1-470">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2f7c1-471">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2f7c1-472">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-472">All transports are enabled by default.</span></span>

<span data-ttu-id="2f7c1-473">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2f7c1-474">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2f7c1-475">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2f7c1-476">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2f7c1-477">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2f7c1-478">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2f7c1-479">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-479">Configure bearer authentication</span></span>

<span data-ttu-id="2f7c1-480">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2f7c1-481">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2f7c1-482">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2f7c1-483">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2f7c1-484">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2f7c1-485">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2f7c1-486">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2f7c1-487">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2f7c1-488">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2f7c1-489">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2f7c1-490">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-491">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-492">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-492">Option</span></span> | <span data-ttu-id="2f7c1-493">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-493">Default value</span></span> | <span data-ttu-id="2f7c1-494">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2f7c1-495">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-496">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-496">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-497">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-498">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-499">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-500">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-500">15 seconds</span></span> | <span data-ttu-id="2f7c1-501">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-502">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-503">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-504">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-505">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-505">15 seconds</span></span> | <span data-ttu-id="2f7c1-506">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-507">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-508">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2f7c1-509">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-511">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-511">Option</span></span> | <span data-ttu-id="2f7c1-512">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-512">Default value</span></span> | <span data-ttu-id="2f7c1-513">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2f7c1-514">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-515">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-515">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-516">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2f7c1-517">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-518">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2f7c1-519">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-520">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-521">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-522">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-523">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-523">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-524">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-524">Option</span></span> | <span data-ttu-id="2f7c1-525">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-525">Default value</span></span> | <span data-ttu-id="2f7c1-526">説明</span><span class="sxs-lookup"><span data-stu-id="2f7c1-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2f7c1-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2f7c1-528">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-529">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-529">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-530">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-531">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-532">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2f7c1-533">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-533">15 seconds</span></span> | <span data-ttu-id="2f7c1-534">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-535">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-536">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-537">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2f7c1-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2f7c1-539">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-540">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-541">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-542">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2f7c1-543">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-543">Configure additional options</span></span>

<span data-ttu-id="2f7c1-544">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-545">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-546">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-546">.NET Option</span></span> |  <span data-ttu-id="2f7c1-547">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-547">Default value</span></span> | <span data-ttu-id="2f7c1-548">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-549">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2f7c1-550">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-551">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-552">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2f7c1-553">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-553">Empty</span></span> | <span data-ttu-id="2f7c1-554">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2f7c1-555">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-555">Empty</span></span> | <span data-ttu-id="2f7c1-556">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2f7c1-557">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-557">Empty</span></span> | <span data-ttu-id="2f7c1-558">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2f7c1-559">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-559">5 seconds</span></span> | <span data-ttu-id="2f7c1-560">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-560">WebSockets only.</span></span> <span data-ttu-id="2f7c1-561">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2f7c1-562">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2f7c1-563">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-563">Empty</span></span> | <span data-ttu-id="2f7c1-564">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2f7c1-565">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2f7c1-566">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="2f7c1-567">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2f7c1-568">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2f7c1-569">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="2f7c1-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2f7c1-570">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2f7c1-571">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2f7c1-572">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2f7c1-573">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2f7c1-574">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-576">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-576">JavaScript Option</span></span> | <span data-ttu-id="2f7c1-577">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-577">Default Value</span></span> | <span data-ttu-id="2f7c1-578">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2f7c1-579">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2f7c1-580">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2f7c1-581">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-582">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-583">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-584">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-584">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-585">Java オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-585">Java Option</span></span> | <span data-ttu-id="2f7c1-586">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-586">Default Value</span></span> | <span data-ttu-id="2f7c1-587">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-588">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2f7c1-589">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-590">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-591">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2f7c1-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2f7c1-593">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-593">Empty</span></span> | <span data-ttu-id="2f7c1-594">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2f7c1-595">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2f7c1-596">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2f7c1-597">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2f7c1-598">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2f7c1-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2f7c1-599">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-600">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2f7c1-601">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2f7c1-602">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2f7c1-603">`AddJsonProtocol`は、 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2f7c1-604">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2f7c1-605">そのオブジェクトの[PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions)プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2f7c1-606">詳細については、[ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2f7c1-607">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="2f7c1-608">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2f7c1-609">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2f7c1-610">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2f7c1-611">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="2f7c1-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2f7c1-612">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2f7c1-613">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-613">MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-614">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2f7c1-615">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-616">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2f7c1-617">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-617">Configure server options</span></span>

<span data-ttu-id="2f7c1-618">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2f7c1-619">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-619">Option</span></span> | <span data-ttu-id="2f7c1-620">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-620">Default Value</span></span> | <span data-ttu-id="2f7c1-621">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2f7c1-622">30 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-622">30 seconds</span></span> | <span data-ttu-id="2f7c1-623">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2f7c1-624">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2f7c1-625">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-626">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-626">15 seconds</span></span> | <span data-ttu-id="2f7c1-627">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2f7c1-628">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-629">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-630">15 seconds</span></span> | <span data-ttu-id="2f7c1-631">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2f7c1-632">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2f7c1-633">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2f7c1-634">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="2f7c1-634">All installed protocols</span></span> | <span data-ttu-id="2f7c1-635">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-635">Protocols supported by this hub.</span></span> <span data-ttu-id="2f7c1-636">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2f7c1-637">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2f7c1-638">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2f7c1-639">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2f7c1-640">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2f7c1-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-641">32 KB</span></span> | <span data-ttu-id="2f7c1-642">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2f7c1-643">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2f7c1-644">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2f7c1-645">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2f7c1-646">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2f7c1-647">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2f7c1-648">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2f7c1-649">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-649">Option</span></span> | <span data-ttu-id="2f7c1-650">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-650">Default Value</span></span> | <span data-ttu-id="2f7c1-651">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2f7c1-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-652">32 KB</span></span> | <span data-ttu-id="2f7c1-653">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2f7c1-654">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2f7c1-655">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2f7c1-656">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2f7c1-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-657">32 KB</span></span> | <span data-ttu-id="2f7c1-658">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2f7c1-659">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2f7c1-660">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-660">All Transports are enabled.</span></span> | <span data-ttu-id="2f7c1-661">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2f7c1-662">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-662">See below.</span></span> | <span data-ttu-id="2f7c1-663">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2f7c1-664">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-664">See below.</span></span> | <span data-ttu-id="2f7c1-665">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2f7c1-666">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2f7c1-667">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-667">Option</span></span> | <span data-ttu-id="2f7c1-668">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-668">Default Value</span></span> | <span data-ttu-id="2f7c1-669">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2f7c1-670">90秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-670">90 seconds</span></span> | <span data-ttu-id="2f7c1-671">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2f7c1-672">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2f7c1-673">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2f7c1-674">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-674">Option</span></span> | <span data-ttu-id="2f7c1-675">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-675">Default Value</span></span> | <span data-ttu-id="2f7c1-676">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2f7c1-677">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-677">5 seconds</span></span> | <span data-ttu-id="2f7c1-678">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2f7c1-679">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2f7c1-680">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2f7c1-681">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-681">Configure client options</span></span>

<span data-ttu-id="2f7c1-682">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2f7c1-683">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2f7c1-684">ログの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-684">Configure logging</span></span>

<span data-ttu-id="2f7c1-685">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2f7c1-686">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2f7c1-687">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-688">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2f7c1-689">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2f7c1-690">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2f7c1-691">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2f7c1-692">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2f7c1-693">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2f7c1-694">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2f7c1-695">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2f7c1-696">これは SignalR 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2f7c1-697">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-697">The following table lists the available log levels.</span></span> <span data-ttu-id="2f7c1-698">`configureLogging`ログに記録される**最小**ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2f7c1-699">このレベルでログに記録されたメッセージ、**またはテーブルに記載されているレベルの**メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2f7c1-700">String</span><span class="sxs-lookup"><span data-stu-id="2f7c1-700">String</span></span>                      | <span data-ttu-id="2f7c1-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2f7c1-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2f7c1-702">`info` **または** `information`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2f7c1-703">`warn` **または** `warning`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2f7c1-704">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2f7c1-705">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2f7c1-706">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2f7c1-707">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2f7c1-708">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2f7c1-709">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2f7c1-710">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2f7c1-711">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-711">Configure allowed transports</span></span>

<span data-ttu-id="2f7c1-712">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2f7c1-713">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2f7c1-714">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-714">All transports are enabled by default.</span></span>

<span data-ttu-id="2f7c1-715">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2f7c1-716">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2f7c1-717">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2f7c1-718">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2f7c1-719">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2f7c1-720">Java クライアントでは、トランスポートフォールバックはまだサポートされてい SignalR ません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2f7c1-721">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-721">Configure bearer authentication</span></span>

<span data-ttu-id="2f7c1-722">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2f7c1-723">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2f7c1-724">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2f7c1-725">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2f7c1-726">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2f7c1-727">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2f7c1-728">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2f7c1-729">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2f7c1-730">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2f7c1-731">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2f7c1-732">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-733">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-734">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-734">Option</span></span> | <span data-ttu-id="2f7c1-735">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-735">Default value</span></span> | <span data-ttu-id="2f7c1-736">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2f7c1-737">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-738">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-738">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-739">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-740">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-741">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-742">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-742">15 seconds</span></span> | <span data-ttu-id="2f7c1-743">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-744">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-745">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-746">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-747">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-747">15 seconds</span></span> | <span data-ttu-id="2f7c1-748">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-749">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-750">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2f7c1-751">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-753">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-753">Option</span></span> | <span data-ttu-id="2f7c1-754">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-754">Default value</span></span> | <span data-ttu-id="2f7c1-755">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2f7c1-756">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-757">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-757">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-758">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2f7c1-759">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-760">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2f7c1-761">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-762">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-763">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-764">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-765">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-765">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-766">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-766">Option</span></span> | <span data-ttu-id="2f7c1-767">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-767">Default value</span></span> | <span data-ttu-id="2f7c1-768">説明</span><span class="sxs-lookup"><span data-stu-id="2f7c1-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2f7c1-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2f7c1-770">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-771">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-771">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-772">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-773">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-774">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2f7c1-775">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-775">15 seconds</span></span> | <span data-ttu-id="2f7c1-776">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-777">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-778">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-779">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2f7c1-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2f7c1-781">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-782">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-783">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-784">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2f7c1-785">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-785">Configure additional options</span></span>

<span data-ttu-id="2f7c1-786">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-787">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-788">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-788">.NET Option</span></span> |  <span data-ttu-id="2f7c1-789">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-789">Default value</span></span> | <span data-ttu-id="2f7c1-790">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-791">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2f7c1-792">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-793">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-794">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2f7c1-795">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-795">Empty</span></span> | <span data-ttu-id="2f7c1-796">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2f7c1-797">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-797">Empty</span></span> | <span data-ttu-id="2f7c1-798">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2f7c1-799">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-799">Empty</span></span> | <span data-ttu-id="2f7c1-800">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2f7c1-801">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-801">5 seconds</span></span> | <span data-ttu-id="2f7c1-802">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-802">WebSockets only.</span></span> <span data-ttu-id="2f7c1-803">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2f7c1-804">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2f7c1-805">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-805">Empty</span></span> | <span data-ttu-id="2f7c1-806">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2f7c1-807">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2f7c1-808">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="2f7c1-809">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2f7c1-810">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2f7c1-811">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="2f7c1-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2f7c1-812">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2f7c1-813">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2f7c1-814">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2f7c1-815">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2f7c1-816">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-818">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-818">JavaScript Option</span></span> | <span data-ttu-id="2f7c1-819">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-819">Default Value</span></span> | <span data-ttu-id="2f7c1-820">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2f7c1-821">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2f7c1-822">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2f7c1-823">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-824">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-825">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-826">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-826">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-827">Java オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-827">Java Option</span></span> | <span data-ttu-id="2f7c1-828">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-828">Default Value</span></span> | <span data-ttu-id="2f7c1-829">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-830">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2f7c1-831">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-832">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-833">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2f7c1-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2f7c1-835">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-835">Empty</span></span> | <span data-ttu-id="2f7c1-836">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2f7c1-837">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2f7c1-838">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2f7c1-839">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2f7c1-840">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2f7c1-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2f7c1-841">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-842">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2f7c1-843">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2f7c1-844">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドの[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2f7c1-845">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2f7c1-846">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2f7c1-847">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="2f7c1-848">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="2f7c1-849">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2f7c1-850">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2f7c1-851">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2f7c1-852">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-852">MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-853">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2f7c1-854">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-855">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2f7c1-856">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-856">Configure server options</span></span>

<span data-ttu-id="2f7c1-857">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2f7c1-858">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-858">Option</span></span> | <span data-ttu-id="2f7c1-859">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-859">Default Value</span></span> | <span data-ttu-id="2f7c1-860">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2f7c1-861">30 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-861">30 seconds</span></span> | <span data-ttu-id="2f7c1-862">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2f7c1-863">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2f7c1-864">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-865">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-865">15 seconds</span></span> | <span data-ttu-id="2f7c1-866">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2f7c1-867">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-868">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-869">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-869">15 seconds</span></span> | <span data-ttu-id="2f7c1-870">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2f7c1-871">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2f7c1-872">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2f7c1-873">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="2f7c1-873">All installed protocols</span></span> | <span data-ttu-id="2f7c1-874">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-874">Protocols supported by this hub.</span></span> <span data-ttu-id="2f7c1-875">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2f7c1-876">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2f7c1-877">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="2f7c1-878">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2f7c1-879">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2f7c1-880">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2f7c1-881">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2f7c1-882">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2f7c1-883">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2f7c1-884">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-884">Option</span></span> | <span data-ttu-id="2f7c1-885">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-885">Default Value</span></span> | <span data-ttu-id="2f7c1-886">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2f7c1-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-887">32 KB</span></span> | <span data-ttu-id="2f7c1-888">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="2f7c1-889">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2f7c1-890">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2f7c1-891">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2f7c1-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-892">32 KB</span></span> | <span data-ttu-id="2f7c1-893">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="2f7c1-894">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2f7c1-895">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-895">All Transports are enabled.</span></span> | <span data-ttu-id="2f7c1-896">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2f7c1-897">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-897">See below.</span></span> | <span data-ttu-id="2f7c1-898">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2f7c1-899">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-899">See below.</span></span> | <span data-ttu-id="2f7c1-900">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2f7c1-901">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2f7c1-902">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-902">Option</span></span> | <span data-ttu-id="2f7c1-903">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-903">Default Value</span></span> | <span data-ttu-id="2f7c1-904">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2f7c1-905">90秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-905">90 seconds</span></span> | <span data-ttu-id="2f7c1-906">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2f7c1-907">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2f7c1-908">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2f7c1-909">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-909">Option</span></span> | <span data-ttu-id="2f7c1-910">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-910">Default Value</span></span> | <span data-ttu-id="2f7c1-911">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2f7c1-912">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-912">5 seconds</span></span> | <span data-ttu-id="2f7c1-913">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2f7c1-914">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2f7c1-915">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2f7c1-916">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-916">Configure client options</span></span>

<span data-ttu-id="2f7c1-917">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2f7c1-918">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2f7c1-919">ログの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-919">Configure logging</span></span>

<span data-ttu-id="2f7c1-920">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2f7c1-921">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2f7c1-922">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-923">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2f7c1-924">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2f7c1-925">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2f7c1-926">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2f7c1-927">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2f7c1-928">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2f7c1-929">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2f7c1-930">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2f7c1-931">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2f7c1-932">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2f7c1-933">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2f7c1-934">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2f7c1-935">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2f7c1-936">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2f7c1-937">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-937">Configure allowed transports</span></span>

<span data-ttu-id="2f7c1-938">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2f7c1-939">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2f7c1-940">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-940">All transports are enabled by default.</span></span>

<span data-ttu-id="2f7c1-941">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2f7c1-942">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2f7c1-943">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2f7c1-944">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-944">Configure bearer authentication</span></span>

<span data-ttu-id="2f7c1-945">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2f7c1-946">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2f7c1-947">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2f7c1-948">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2f7c1-949">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2f7c1-950">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2f7c1-951">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2f7c1-952">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2f7c1-953">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2f7c1-954">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2f7c1-955">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-956">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-957">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-957">Option</span></span> | <span data-ttu-id="2f7c1-958">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-958">Default value</span></span> | <span data-ttu-id="2f7c1-959">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2f7c1-960">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-961">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-961">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-962">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-963">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-964">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-965">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-965">15 seconds</span></span> | <span data-ttu-id="2f7c1-966">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-967">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-968">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-969">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-970">15 seconds</span></span> | <span data-ttu-id="2f7c1-971">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-972">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-973">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2f7c1-974">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-976">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-976">Option</span></span> | <span data-ttu-id="2f7c1-977">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-977">Default value</span></span> | <span data-ttu-id="2f7c1-978">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2f7c1-979">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-980">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-980">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-981">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2f7c1-982">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-983">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2f7c1-984">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-985">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-986">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-987">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-988">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-988">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-989">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-989">Option</span></span> | <span data-ttu-id="2f7c1-990">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-990">Default value</span></span> | <span data-ttu-id="2f7c1-991">説明</span><span class="sxs-lookup"><span data-stu-id="2f7c1-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2f7c1-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2f7c1-993">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-994">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-994">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-995">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-996">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-997">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2f7c1-998">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-998">15 seconds</span></span> | <span data-ttu-id="2f7c1-999">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-1000">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-1001">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-1002">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2f7c1-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2f7c1-1004">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-1005">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2f7c1-1006">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2f7c1-1007">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2f7c1-1008">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1008">Configure additional options</span></span>

<span data-ttu-id="2f7c1-1009">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-1011">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1011">.NET Option</span></span> |  <span data-ttu-id="2f7c1-1012">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1012">Default value</span></span> | <span data-ttu-id="2f7c1-1013">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-1014">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2f7c1-1015">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-1016">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-1017">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2f7c1-1018">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1018">Empty</span></span> | <span data-ttu-id="2f7c1-1019">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2f7c1-1020">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1020">Empty</span></span> | <span data-ttu-id="2f7c1-1021">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2f7c1-1022">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1022">Empty</span></span> | <span data-ttu-id="2f7c1-1023">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2f7c1-1024">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1024">5 seconds</span></span> | <span data-ttu-id="2f7c1-1025">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1025">WebSockets only.</span></span> <span data-ttu-id="2f7c1-1026">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2f7c1-1027">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2f7c1-1028">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1028">Empty</span></span> | <span data-ttu-id="2f7c1-1029">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2f7c1-1030">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2f7c1-1031">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="2f7c1-1032">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2f7c1-1033">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2f7c1-1034">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2f7c1-1035">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2f7c1-1036">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2f7c1-1037">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2f7c1-1038">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2f7c1-1039">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-1041">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1041">JavaScript Option</span></span> | <span data-ttu-id="2f7c1-1042">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1042">Default Value</span></span> | <span data-ttu-id="2f7c1-1043">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2f7c1-1044">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2f7c1-1045">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2f7c1-1046">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-1047">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-1048">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-1049">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-1050">Java オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1050">Java Option</span></span> | <span data-ttu-id="2f7c1-1051">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1051">Default Value</span></span> | <span data-ttu-id="2f7c1-1052">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-1053">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2f7c1-1054">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-1055">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-1056">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2f7c1-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2f7c1-1058">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1058">Empty</span></span> | <span data-ttu-id="2f7c1-1059">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2f7c1-1060">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2f7c1-1061">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2f7c1-1062">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2f7c1-1063">その他の資料</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2f7c1-1064">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-1065">ASP.NET Core は SignalR 、 [JSON](https://www.json.org/)と[messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2f7c1-1066">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2f7c1-1067">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドの[AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2f7c1-1068">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2f7c1-1069">そのオブジェクトの[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings)プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2f7c1-1070">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="2f7c1-1071">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="2f7c1-1072">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2f7c1-1073">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2f7c1-1074">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2f7c1-1075">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1075">MessagePack serialization options</span></span>

<span data-ttu-id="2f7c1-1076">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2f7c1-1077">詳細については[、「Messagepack」を SignalR ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-1078">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2f7c1-1079">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1079">Configure server options</span></span>

<span data-ttu-id="2f7c1-1080">次の表では、ハブを構成するためのオプションについて説明し SignalR ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2f7c1-1081">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1081">Option</span></span> | <span data-ttu-id="2f7c1-1082">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1082">Default Value</span></span> | <span data-ttu-id="2f7c1-1083">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-1084">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1084">15 seconds</span></span> | <span data-ttu-id="2f7c1-1085">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2f7c1-1086">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-1087">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2f7c1-1088">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1088">15 seconds</span></span> | <span data-ttu-id="2f7c1-1089">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2f7c1-1090">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2f7c1-1091">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2f7c1-1092">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1092">All installed protocols</span></span> | <span data-ttu-id="2f7c1-1093">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="2f7c1-1094">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2f7c1-1095">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2f7c1-1096">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="2f7c1-1097">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `AddSignalR` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2f7c1-1098">1つのハブのオプションは、で提供されるグローバルオプションを上書き `AddSignalR` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2f7c1-1099">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2f7c1-1100">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2f7c1-1101">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="2f7c1-1102">次の表では、ASP.NET Core SignalR の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2f7c1-1103">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1103">Option</span></span> | <span data-ttu-id="2f7c1-1104">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1104">Default Value</span></span> | <span data-ttu-id="2f7c1-1105">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2f7c1-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1106">32 KB</span></span> | <span data-ttu-id="2f7c1-1107">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="2f7c1-1108">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2f7c1-1109">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2f7c1-1110">クライアントがハブへの接続を承認されているかどうかを判断するために使用される[Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata)オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2f7c1-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1111">32 KB</span></span> | <span data-ttu-id="2f7c1-1112">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="2f7c1-1113">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2f7c1-1114">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1114">All Transports are enabled.</span></span> | <span data-ttu-id="2f7c1-1115">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2f7c1-1116">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1116">See below.</span></span> | <span data-ttu-id="2f7c1-1117">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2f7c1-1118">以下を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1118">See below.</span></span> | <span data-ttu-id="2f7c1-1119">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2f7c1-1120">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2f7c1-1121">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1121">Option</span></span> | <span data-ttu-id="2f7c1-1122">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1122">Default Value</span></span> | <span data-ttu-id="2f7c1-1123">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2f7c1-1124">90秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1124">90 seconds</span></span> | <span data-ttu-id="2f7c1-1125">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2f7c1-1126">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2f7c1-1127">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2f7c1-1128">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1128">Option</span></span> | <span data-ttu-id="2f7c1-1129">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1129">Default Value</span></span> | <span data-ttu-id="2f7c1-1130">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2f7c1-1131">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1131">5 seconds</span></span> | <span data-ttu-id="2f7c1-1132">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2f7c1-1133">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2f7c1-1134">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2f7c1-1135">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1135">Configure client options</span></span>

<span data-ttu-id="2f7c1-1136">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2f7c1-1137">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2f7c1-1138">ログの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1138">Configure logging</span></span>

<span data-ttu-id="2f7c1-1139">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2f7c1-1140">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2f7c1-1141">詳細については、ASP.NET Core のドキュメントの[ログ](xref:fundamentals/logging/index)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2f7c1-1142">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2f7c1-1143">完全な一覧については、ドキュメントの「[組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2f7c1-1144">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2f7c1-1145">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2f7c1-1146">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2f7c1-1147">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2f7c1-1148">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2f7c1-1149">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2f7c1-1150">ログ記録の詳細については、 [ SignalR 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2f7c1-1151">SignalRJava クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2f7c1-1152">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2f7c1-1153">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` SignalR います。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2f7c1-1154">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2f7c1-1155">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2f7c1-1156">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1156">Configure allowed transports</span></span>

<span data-ttu-id="2f7c1-1157">によって使用されるトランスポートは、 SignalR `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2f7c1-1158">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2f7c1-1159">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="2f7c1-1160">たとえば、サーバーから送信されたイベントトランスポートを無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2f7c1-1161">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2f7c1-1162">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1162">Configure bearer authentication</span></span>

<span data-ttu-id="2f7c1-1163">要求と共に認証データを提供するには SignalR 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2f7c1-1164">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2f7c1-1165">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (特に、サーバーが送信するイベントと Websocket 要求) を適用する機能を制限する場合は**例外**です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2f7c1-1166">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2f7c1-1167">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2f7c1-1168">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="2f7c1-1169">Java クライアントでは、 SignalR [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2f7c1-1170">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2f7c1-1171">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2f7c1-1172">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2f7c1-1173">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-1175">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1175">Option</span></span> | <span data-ttu-id="2f7c1-1176">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1176">Default value</span></span> | <span data-ttu-id="2f7c1-1177">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2f7c1-1178">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-1179">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1179">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-1180">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-1181">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-1182">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2f7c1-1183">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1183">15 seconds</span></span> | <span data-ttu-id="2f7c1-1184">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-1185">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2f7c1-1186">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-1187">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="2f7c1-1188">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-1190">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1190">Option</span></span> | <span data-ttu-id="2f7c1-1191">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1191">Default value</span></span> | <span data-ttu-id="2f7c1-1192">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2f7c1-1193">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-1194">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1194">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-1195">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2f7c1-1196">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-1197">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-1198">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-1199">オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1199">Option</span></span> | <span data-ttu-id="2f7c1-1200">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1200">Default value</span></span> | <span data-ttu-id="2f7c1-1201">説明</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2f7c1-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2f7c1-1203">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2f7c1-1204">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1204">Timeout for server activity.</span></span> <span data-ttu-id="2f7c1-1205">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-1206">この値は、ping メッセージをサーバーから送信**し**、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2f7c1-1207">推奨値は、サーバーの値の少なくとも2倍の数で、 `KeepAliveInterval` ping が到着するまでの時間を確保します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2f7c1-1208">15 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1208">15 seconds</span></span> | <span data-ttu-id="2f7c1-1209">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="2f7c1-1210">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2f7c1-1211">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2f7c1-1212">ハンドシェイクプロセスの詳細については、「 [ SignalR Hub プロトコルの仕様](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2f7c1-1213">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1213">Configure additional options</span></span>

<span data-ttu-id="2f7c1-1214">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2f7c1-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2f7c1-1216">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1216">.NET Option</span></span> |  <span data-ttu-id="2f7c1-1217">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1217">Default value</span></span> | <span data-ttu-id="2f7c1-1218">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-1219">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2f7c1-1220">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-1221">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-1222">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2f7c1-1223">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1223">Empty</span></span> | <span data-ttu-id="2f7c1-1224">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2f7c1-1225">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1225">Empty</span></span> | <span data-ttu-id="2f7c1-1226">すべての HTTP 要求と共に送信する HTTP クッキーのコレクション。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2f7c1-1227">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1227">Empty</span></span> | <span data-ttu-id="2f7c1-1228">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2f7c1-1229">5 秒</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1229">5 seconds</span></span> | <span data-ttu-id="2f7c1-1230">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1230">WebSockets only.</span></span> <span data-ttu-id="2f7c1-1231">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2f7c1-1232">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2f7c1-1233">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1233">Empty</span></span> | <span data-ttu-id="2f7c1-1234">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2f7c1-1235">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2f7c1-1236">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="2f7c1-1237">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2f7c1-1238">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2f7c1-1239">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション (Cookie やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2f7c1-1240">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2f7c1-1241">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2f7c1-1242">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2f7c1-1243">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2f7c1-1244">オプションの構成に使用できる[ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions)のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2f7c1-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2f7c1-1246">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1246">JavaScript Option</span></span> | <span data-ttu-id="2f7c1-1247">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1247">Default Value</span></span> | <span data-ttu-id="2f7c1-1248">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2f7c1-1249">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2f7c1-1250">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2f7c1-1251">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-1252">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-1253">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2f7c1-1254">Java</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="2f7c1-1255">Java オプション</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1255">Java Option</span></span> | <span data-ttu-id="2f7c1-1256">既定値</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1256">Default Value</span></span> | <span data-ttu-id="2f7c1-1257">[説明]</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2f7c1-1258">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2f7c1-1259">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2f7c1-1260">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ**ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2f7c1-1261">Azure サービスを使用している場合、この設定を有効にすることはできません SignalR 。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2f7c1-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2f7c1-1263">空</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1263">Empty</span></span> | <span data-ttu-id="2f7c1-1264">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2f7c1-1265">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2f7c1-1266">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2f7c1-1267">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2f7c1-1268">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2f7c1-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
