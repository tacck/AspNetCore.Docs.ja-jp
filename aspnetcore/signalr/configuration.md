---
title: 'ASP.NET Core :::no-loc(SignalR)::: の構成'
author: bradygaster
description: 'ASP.NET Core アプリを構成する方法について説明 :::no-loc(SignalR)::: します。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/configuration
ms.openlocfilehash: 7dac8c84683553a52e07ecc61c8bcf8616e77dc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061237"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="afba3-103">ASP.NET Core :::no-loc(SignalR)::: の構成</span><span class="sxs-lookup"><span data-stu-id="afba3-103">ASP.NET Core :::no-loc(SignalR)::: configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="afba3-104">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="afba3-105">ASP.NET Core は :::no-loc(SignalR)::: 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="afba3-105">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="afba3-106">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="afba3-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="afba3-107">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="afba3-108">`AddJsonProtocol`は、の[ :::no-loc(SignalR)::: Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-108">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="afba3-109">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="afba3-110">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="afba3-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="afba3-111">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="afba3-112">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="afba3-113">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="afba3-114">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="afba3-115">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="afba3-116">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="afba3-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="afba3-117">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="afba3-118">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-118">MessagePack serialization options</span></span>

<span data-ttu-id="afba3-119">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="afba3-120">詳細については[、「Messagepack」を :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-120">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-121">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="afba3-122">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-122">Configure server options</span></span>

<span data-ttu-id="afba3-123">次の表では、ハブを構成するためのオプションについて説明し :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-123">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="afba3-124">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-124">Option</span></span> | <span data-ttu-id="afba3-125">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-125">Default Value</span></span> | <span data-ttu-id="afba3-126">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="afba3-127">30 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-127">30 seconds</span></span> | <span data-ttu-id="afba3-128">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="afba3-129">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="afba3-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="afba3-130">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="afba3-131">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-131">15 seconds</span></span> | <span data-ttu-id="afba3-132">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="afba3-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="afba3-133">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-134">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-134">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-135">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-135">15 seconds</span></span> | <span data-ttu-id="afba3-136">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="afba3-137">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="afba3-138">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="afba3-139">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="afba3-139">All installed protocols</span></span> | <span data-ttu-id="afba3-140">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="afba3-140">Protocols supported by this hub.</span></span> <span data-ttu-id="afba3-141">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="afba3-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="afba3-142">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="afba3-143">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="afba3-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="afba3-144">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="afba3-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="afba3-145">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="afba3-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-146">32 KB</span></span> | <span data-ttu-id="afba3-147">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="afba3-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="afba3-148">1</span><span class="sxs-lookup"><span data-stu-id="afba3-148">1</span></span> | <span data-ttu-id="afba3-149">各クライアントがキューに入れる前に並列で呼び出すことができるハブメソッドの最大数。</span><span class="sxs-lookup"><span data-stu-id="afba3-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="afba3-150">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-150">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="afba3-151">1つのハブのオプションは、で提供されるグローバルオプションを上書き `Add:::no-loc(SignalR):::` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-151">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="afba3-152">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="afba3-153">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="afba3-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="afba3-154">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="afba3-155">次の表では、ASP.NET Core :::no-loc(SignalR)::: の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="afba3-155">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="afba3-156">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-156">Option</span></span> | <span data-ttu-id="afba3-157">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-157">Default Value</span></span> | <span data-ttu-id="afba3-158">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="afba3-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-159">32 KB</span></span> | <span data-ttu-id="afba3-160">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="afba3-161">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="afba3-162">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="afba3-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="afba3-163">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="afba3-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="afba3-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-164">32 KB</span></span> | <span data-ttu-id="afba3-165">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="afba3-166">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="afba3-167">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="afba3-167">All Transports are enabled.</span></span> | <span data-ttu-id="afba3-168">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="afba3-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="afba3-169">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-169">See below.</span></span> | <span data-ttu-id="afba3-170">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="afba3-171">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-171">See below.</span></span> | <span data-ttu-id="afba3-172">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="afba3-173">0</span><span class="sxs-lookup"><span data-stu-id="afba3-173">0</span></span> | <span data-ttu-id="afba3-174">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="afba3-175">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="afba3-176">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="afba3-177">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-177">Option</span></span> | <span data-ttu-id="afba3-178">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-178">Default Value</span></span> | <span data-ttu-id="afba3-179">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="afba3-180">90秒</span><span class="sxs-lookup"><span data-stu-id="afba3-180">90 seconds</span></span> | <span data-ttu-id="afba3-181">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="afba3-182">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="afba3-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="afba3-183">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="afba3-184">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-184">Option</span></span> | <span data-ttu-id="afba3-185">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-185">Default Value</span></span> | <span data-ttu-id="afba3-186">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="afba3-187">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-187">5 seconds</span></span> | <span data-ttu-id="afba3-188">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="afba3-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="afba3-189">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="afba3-190">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="afba3-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="afba3-191">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-191">Configure client options</span></span>

<span data-ttu-id="afba3-192">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="afba3-193">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="afba3-194">ログの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-194">Configure logging</span></span>

<span data-ttu-id="afba3-195">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="afba3-196">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="afba3-197">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-198">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="afba3-199">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="afba3-200">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="afba3-201">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afba3-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="afba3-202">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="afba3-203">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="afba3-204">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="afba3-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="afba3-205">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="afba3-206">これは :::no-loc(SignalR)::: 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-206">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="afba3-207">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="afba3-207">The following table lists the available log levels.</span></span> <span data-ttu-id="afba3-208">`configureLogging`ログに記録される **最小** ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="afba3-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="afba3-209">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの** メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-209">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="afba3-210">String</span><span class="sxs-lookup"><span data-stu-id="afba3-210">String</span></span>                      | <span data-ttu-id="afba3-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="afba3-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="afba3-212">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="afba3-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="afba3-213">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="afba3-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="afba3-214">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="afba3-215">ログ記録の詳細については、 [ :::no-loc(SignalR)::: 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-215">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="afba3-216">:::no-loc(SignalR):::Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="afba3-216">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="afba3-217">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="afba3-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="afba3-218">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` :::no-loc(SignalR)::: います。</span><span class="sxs-lookup"><span data-stu-id="afba3-218">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="afba3-219">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="afba3-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="afba3-220">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="afba3-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="afba3-221">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-221">Configure allowed transports</span></span>

<span data-ttu-id="afba3-222">によって使用されるトランスポートは、 :::no-loc(SignalR)::: `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-222">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="afba3-223">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="afba3-224">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="afba3-224">All transports are enabled by default.</span></span>

<span data-ttu-id="afba3-225">たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="afba3-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="afba3-226">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="afba3-227">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="afba3-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="afba3-228">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="afba3-229">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="afba3-230">Java クライアントでは、トランスポートフォールバックはまだサポートされてい :::no-loc(SignalR)::: ません。</span><span class="sxs-lookup"><span data-stu-id="afba3-230">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="afba3-231">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-231">Configure bearer authentication</span></span>

<span data-ttu-id="afba3-232">要求と共に認証データを提供するには :::no-loc(SignalR)::: 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-232">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="afba3-233">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="afba3-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="afba3-234">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="afba3-235">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="afba3-236">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="afba3-237">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="afba3-238">Java クライアントでは、 :::no-loc(SignalR)::: [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-238">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="afba3-239">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="afba3-240">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="afba3-241">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="afba3-242">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-243">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-244">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-244">Option</span></span> | <span data-ttu-id="afba3-245">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-245">Default value</span></span> | <span data-ttu-id="afba3-246">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="afba3-247">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-248">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-248">Timeout for server activity.</span></span> <span data-ttu-id="afba3-249">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-250">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-251">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="afba3-252">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-252">15 seconds</span></span> | <span data-ttu-id="afba3-253">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-254">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-255">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-256">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-256">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-257">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-257">15 seconds</span></span> | <span data-ttu-id="afba3-258">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-259">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-260">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="afba3-261">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="afba3-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-263">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-263">Option</span></span> | <span data-ttu-id="afba3-264">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-264">Default value</span></span> | <span data-ttu-id="afba3-265">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="afba3-266">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-267">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-267">Timeout for server activity.</span></span> <span data-ttu-id="afba3-268">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="afba3-269">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-270">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="afba3-271">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="afba3-272">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-273">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-274">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-275">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-275">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-276">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-276">Option</span></span> | <span data-ttu-id="afba3-277">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-277">Default value</span></span> | <span data-ttu-id="afba3-278">説明</span><span class="sxs-lookup"><span data-stu-id="afba3-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="afba3-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="afba3-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="afba3-280">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-281">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-281">Timeout for server activity.</span></span> <span data-ttu-id="afba3-282">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-283">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-284">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="afba3-285">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-285">15 seconds</span></span> | <span data-ttu-id="afba3-286">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-287">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-288">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-289">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-289">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="afba3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="afba3-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="afba3-291">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="afba3-292">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-293">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-294">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="afba3-295">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-295">Configure additional options</span></span>

<span data-ttu-id="afba3-296">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-297">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-298">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-298">.NET Option</span></span> |  <span data-ttu-id="afba3-299">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-299">Default value</span></span> | <span data-ttu-id="afba3-300">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="afba3-301">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="afba3-302">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-303">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-303">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-304">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-304">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="afba3-305">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-305">Empty</span></span> | <span data-ttu-id="afba3-306">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="afba3-307">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-307">Empty</span></span> | <span data-ttu-id="afba3-308">:::no-loc(cookie):::すべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-308">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="afba3-309">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-309">Empty</span></span> | <span data-ttu-id="afba3-310">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="afba3-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="afba3-311">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-311">5 seconds</span></span> | <span data-ttu-id="afba3-312">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="afba3-312">WebSockets only.</span></span> <span data-ttu-id="afba3-313">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="afba3-314">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="afba3-315">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-315">Empty</span></span> | <span data-ttu-id="afba3-316">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="afba3-317">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="afba3-318">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="afba3-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="afba3-319">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="afba3-320">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="afba3-321">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( :::no-loc(Cookie)::: s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="afba3-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="afba3-322">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="afba3-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="afba3-323">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="afba3-324">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="afba3-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="afba3-325">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="afba3-326">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="afba3-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-328">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-328">JavaScript Option</span></span> | <span data-ttu-id="afba3-329">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-329">Default Value</span></span> | <span data-ttu-id="afba3-330">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="afba3-331">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="afba3-332"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-332">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `headers` | `null` | <span data-ttu-id="afba3-333">すべての HTTP 要求と共に送信されるヘッダーのディクショナリ。</span><span class="sxs-lookup"><span data-stu-id="afba3-333">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="afba3-334">ブラウザーでヘッダーを送信することは、Websocket やストリームでは機能しません <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> 。</span><span class="sxs-lookup"><span data-stu-id="afba3-334">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="afba3-335">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-335">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="afba3-336">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-336">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-337">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-337">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-338">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-338">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="afba3-339">CORS 要求と共に資格情報を送信するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-339">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="afba3-340">Azure App Service は :::no-loc(cookie)::: 固定セッションでを使用します。このオプションを正しく動作させるには、このオプションを有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-340">Azure App Service uses :::no-loc(cookie):::s for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="afba3-341">での CORS の詳細につい :::no-loc(SignalR)::: ては、「」を参照してください <xref:signalr/security#cross-origin-resource-sharing> 。</span><span class="sxs-lookup"><span data-stu-id="afba3-341">For more info on CORS with :::no-loc(SignalR):::, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-342">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-342">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-343">Java オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-343">Java Option</span></span> | <span data-ttu-id="afba3-344">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-344">Default Value</span></span> | <span data-ttu-id="afba3-345">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-345">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="afba3-346">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-346">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="afba3-347">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-347">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-348">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-348">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-349">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-349">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="afba3-350">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="afba3-350">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="afba3-351">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-351">Empty</span></span> | <span data-ttu-id="afba3-352">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-352">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="afba3-353">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-353">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.SkipNegotiation = true;
        options.Transports = HttpTransportType.WebSockets;
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="afba3-354">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-354">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        // "Foo: Bar" will not be sent with WebSockets or Server-Sent Events requests
        headers: { "Foo": "Bar" },
        transport: signalR.HttpTransportType.LongPolling 
    })
    .build();
```

<span data-ttu-id="afba3-355">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="afba3-355">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="afba3-356">その他の資料</span><span class="sxs-lookup"><span data-stu-id="afba3-356">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="afba3-357">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-357">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="afba3-358">ASP.NET Core は :::no-loc(SignalR)::: 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="afba3-358">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="afba3-359">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="afba3-359">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="afba3-360">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-360">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="afba3-361">`AddJsonProtocol`は、の[ :::no-loc(SignalR)::: Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-361">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="afba3-362">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-362">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="afba3-363">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="afba3-363">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="afba3-364">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-364">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="afba3-365">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-365">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="afba3-366">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-366">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="afba3-367">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-367">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="afba3-368">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-368">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="afba3-369">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="afba3-369">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="afba3-370">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-370">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="afba3-371">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-371">MessagePack serialization options</span></span>

<span data-ttu-id="afba3-372">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-372">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="afba3-373">詳細については[、「Messagepack」を :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-373">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-374">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-374">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="afba3-375">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-375">Configure server options</span></span>

<span data-ttu-id="afba3-376">次の表では、ハブを構成するためのオプションについて説明し :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-376">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="afba3-377">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-377">Option</span></span> | <span data-ttu-id="afba3-378">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-378">Default Value</span></span> | <span data-ttu-id="afba3-379">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-379">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="afba3-380">30 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-380">30 seconds</span></span> | <span data-ttu-id="afba3-381">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-381">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="afba3-382">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="afba3-382">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="afba3-383">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-383">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="afba3-384">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-384">15 seconds</span></span> | <span data-ttu-id="afba3-385">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="afba3-385">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="afba3-386">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-386">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-387">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-387">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-388">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-388">15 seconds</span></span> | <span data-ttu-id="afba3-389">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-389">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="afba3-390">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-390">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="afba3-391">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-391">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="afba3-392">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="afba3-392">All installed protocols</span></span> | <span data-ttu-id="afba3-393">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="afba3-393">Protocols supported by this hub.</span></span> <span data-ttu-id="afba3-394">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="afba3-394">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="afba3-395">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-395">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="afba3-396">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="afba3-396">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="afba3-397">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="afba3-397">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="afba3-398">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-398">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="afba3-399">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-399">32 KB</span></span> | <span data-ttu-id="afba3-400">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="afba3-400">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="afba3-401">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-401">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="afba3-402">1つのハブのオプションは、で提供されるグローバルオプションを上書き `Add:::no-loc(SignalR):::` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-402">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="afba3-403">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-403">Advanced HTTP configuration options</span></span>

<span data-ttu-id="afba3-404">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="afba3-404">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="afba3-405">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-405">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="afba3-406">次の表では、ASP.NET Core :::no-loc(SignalR)::: の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="afba3-406">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="afba3-407">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-407">Option</span></span> | <span data-ttu-id="afba3-408">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-408">Default Value</span></span> | <span data-ttu-id="afba3-409">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-409">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="afba3-410">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-410">32 KB</span></span> | <span data-ttu-id="afba3-411">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-411">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="afba3-412">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-412">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="afba3-413">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="afba3-413">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="afba3-414">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="afba3-414">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="afba3-415">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-415">32 KB</span></span> | <span data-ttu-id="afba3-416">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-416">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="afba3-417">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-417">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="afba3-418">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="afba3-418">All Transports are enabled.</span></span> | <span data-ttu-id="afba3-419">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="afba3-419">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="afba3-420">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-420">See below.</span></span> | <span data-ttu-id="afba3-421">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-421">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="afba3-422">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-422">See below.</span></span> | <span data-ttu-id="afba3-423">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-423">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="afba3-424">0</span><span class="sxs-lookup"><span data-stu-id="afba3-424">0</span></span> | <span data-ttu-id="afba3-425">Negotiate プロトコルの最小バージョンを指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-425">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="afba3-426">これは、クライアントを新しいバージョンに制限するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-426">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="afba3-427">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-427">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="afba3-428">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-428">Option</span></span> | <span data-ttu-id="afba3-429">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-429">Default Value</span></span> | <span data-ttu-id="afba3-430">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-430">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="afba3-431">90秒</span><span class="sxs-lookup"><span data-stu-id="afba3-431">90 seconds</span></span> | <span data-ttu-id="afba3-432">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-432">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="afba3-433">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="afba3-433">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="afba3-434">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-434">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="afba3-435">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-435">Option</span></span> | <span data-ttu-id="afba3-436">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-436">Default Value</span></span> | <span data-ttu-id="afba3-437">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-437">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="afba3-438">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-438">5 seconds</span></span> | <span data-ttu-id="afba3-439">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="afba3-439">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="afba3-440">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-440">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="afba3-441">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="afba3-441">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="afba3-442">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-442">Configure client options</span></span>

<span data-ttu-id="afba3-443">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-443">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="afba3-444">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-444">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="afba3-445">ログの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-445">Configure logging</span></span>

<span data-ttu-id="afba3-446">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-446">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="afba3-447">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-447">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="afba3-448">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-448">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-449">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-449">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="afba3-450">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-450">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="afba3-451">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-451">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="afba3-452">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afba3-452">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="afba3-453">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-453">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="afba3-454">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-454">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="afba3-455">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="afba3-455">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="afba3-456">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-456">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="afba3-457">これは :::no-loc(SignalR)::: 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-457">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="afba3-458">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="afba3-458">The following table lists the available log levels.</span></span> <span data-ttu-id="afba3-459">`configureLogging`ログに記録される **最小** ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="afba3-459">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="afba3-460">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの** メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-460">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="afba3-461">String</span><span class="sxs-lookup"><span data-stu-id="afba3-461">String</span></span>                      | <span data-ttu-id="afba3-462">LogLevel</span><span class="sxs-lookup"><span data-stu-id="afba3-462">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="afba3-463">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="afba3-463">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="afba3-464">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="afba3-464">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="afba3-465">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-465">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="afba3-466">ログ記録の詳細については、 [ :::no-loc(SignalR)::: 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-466">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="afba3-467">:::no-loc(SignalR):::Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="afba3-467">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="afba3-468">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="afba3-468">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="afba3-469">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` :::no-loc(SignalR)::: います。</span><span class="sxs-lookup"><span data-stu-id="afba3-469">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="afba3-470">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="afba3-470">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="afba3-471">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="afba3-471">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="afba3-472">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-472">Configure allowed transports</span></span>

<span data-ttu-id="afba3-473">によって使用されるトランスポートは、 :::no-loc(SignalR)::: `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-473">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="afba3-474">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-474">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="afba3-475">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="afba3-475">All transports are enabled by default.</span></span>

<span data-ttu-id="afba3-476">たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="afba3-476">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="afba3-477">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-477">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="afba3-478">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="afba3-478">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="afba3-479">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-479">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="afba3-480">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-480">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="afba3-481">Java クライアントでは、トランスポートフォールバックはまだサポートされてい :::no-loc(SignalR)::: ません。</span><span class="sxs-lookup"><span data-stu-id="afba3-481">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="afba3-482">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-482">Configure bearer authentication</span></span>

<span data-ttu-id="afba3-483">要求と共に認証データを提供するには :::no-loc(SignalR)::: 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-483">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="afba3-484">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="afba3-484">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="afba3-485">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-485">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="afba3-486">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-486">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="afba3-487">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-487">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="afba3-488">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-488">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="afba3-489">Java クライアントでは、 :::no-loc(SignalR)::: [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-489">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="afba3-490">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-490">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="afba3-491">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-491">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="afba3-492">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-492">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="afba3-493">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-493">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-494">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-494">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-495">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-495">Option</span></span> | <span data-ttu-id="afba3-496">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-496">Default value</span></span> | <span data-ttu-id="afba3-497">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-497">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="afba3-498">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-498">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-499">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-499">Timeout for server activity.</span></span> <span data-ttu-id="afba3-500">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-500">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-501">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-501">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-502">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-502">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="afba3-503">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-503">15 seconds</span></span> | <span data-ttu-id="afba3-504">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-504">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-505">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-505">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-506">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-506">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-507">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-507">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-508">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-508">15 seconds</span></span> | <span data-ttu-id="afba3-509">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-510">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-511">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="afba3-512">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-512">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="afba3-513">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-513">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-514">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-514">Option</span></span> | <span data-ttu-id="afba3-515">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-515">Default value</span></span> | <span data-ttu-id="afba3-516">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-516">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="afba3-517">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-517">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-518">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-518">Timeout for server activity.</span></span> <span data-ttu-id="afba3-519">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-519">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="afba3-520">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-520">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-521">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-521">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="afba3-522">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-522">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="afba3-523">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-523">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-524">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-524">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-525">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-525">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-526">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-526">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-527">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-527">Option</span></span> | <span data-ttu-id="afba3-528">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-528">Default value</span></span> | <span data-ttu-id="afba3-529">説明</span><span class="sxs-lookup"><span data-stu-id="afba3-529">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="afba3-530">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="afba3-530">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="afba3-531">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-531">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-532">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-532">Timeout for server activity.</span></span> <span data-ttu-id="afba3-533">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-533">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-534">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-534">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-535">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-535">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="afba3-536">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-536">15 seconds</span></span> | <span data-ttu-id="afba3-537">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-537">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-538">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-538">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-539">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-539">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-540">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-540">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="afba3-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="afba3-541">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="afba3-542">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-542">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="afba3-543">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-543">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-544">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-544">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-545">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-545">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="afba3-546">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-546">Configure additional options</span></span>

<span data-ttu-id="afba3-547">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-547">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-548">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-548">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-549">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-549">.NET Option</span></span> |  <span data-ttu-id="afba3-550">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-550">Default value</span></span> | <span data-ttu-id="afba3-551">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-551">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="afba3-552">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-552">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="afba3-553">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-553">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-554">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-554">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-555">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-555">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="afba3-556">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-556">Empty</span></span> | <span data-ttu-id="afba3-557">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-557">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="afba3-558">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-558">Empty</span></span> | <span data-ttu-id="afba3-559">:::no-loc(cookie):::すべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-559">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="afba3-560">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-560">Empty</span></span> | <span data-ttu-id="afba3-561">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="afba3-561">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="afba3-562">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-562">5 seconds</span></span> | <span data-ttu-id="afba3-563">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="afba3-563">WebSockets only.</span></span> <span data-ttu-id="afba3-564">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-564">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="afba3-565">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-565">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="afba3-566">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-566">Empty</span></span> | <span data-ttu-id="afba3-567">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-567">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="afba3-568">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-568">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="afba3-569">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="afba3-569">Not used for WebSocket connections.</span></span> <span data-ttu-id="afba3-570">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-570">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="afba3-571">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-571">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="afba3-572">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( :::no-loc(Cookie)::: s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="afba3-572">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="afba3-573">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="afba3-573">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="afba3-574">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-574">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="afba3-575">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="afba3-575">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="afba3-576">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-576">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="afba3-577">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-577">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="afba3-578">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-578">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-579">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-579">JavaScript Option</span></span> | <span data-ttu-id="afba3-580">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-580">Default Value</span></span> | <span data-ttu-id="afba3-581">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-581">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="afba3-582">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-582">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="afba3-583"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-583">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="afba3-584">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-584">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="afba3-585">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-586">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-586">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-587">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-587">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-588">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-588">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-589">Java オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-589">Java Option</span></span> | <span data-ttu-id="afba3-590">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-590">Default Value</span></span> | <span data-ttu-id="afba3-591">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-591">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="afba3-592">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-592">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="afba3-593">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-593">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-594">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-594">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-595">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-595">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="afba3-596">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="afba3-596">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="afba3-597">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-597">Empty</span></span> | <span data-ttu-id="afba3-598">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-598">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="afba3-599">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-599">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="afba3-600">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-600">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="afba3-601">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="afba3-601">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="afba3-602">その他の資料</span><span class="sxs-lookup"><span data-stu-id="afba3-602">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="afba3-603">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-603">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="afba3-604">ASP.NET Core は :::no-loc(SignalR)::: 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="afba3-604">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="afba3-605">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="afba3-605">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="afba3-606">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) 拡張メソッドを使用して、サーバー上で構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-606">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="afba3-607">`AddJsonProtocol`は、の[ :::no-loc(SignalR)::: Add](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)の後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-607">`AddJsonProtocol` can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="afba3-608">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-608">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="afba3-609">そのオブジェクトの [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) プロパティは、 `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> 引数と戻り値のシリアル化を構成するために使用できるオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="afba3-609">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="afba3-610">詳細については、 [ ドキュメントのSystem.Text.Js](/dotnet/api/system.text.json)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-610">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="afba3-611">たとえば、既定の "キャメルケース" の名前ではなく、プロパティ名の大文字と小文字の区別を変更しないようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-611">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="afba3-612">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-612">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="afba3-613">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-613">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="afba3-614">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-614">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="afba3-615">Newtonsoft.Jsに切り替えます</span><span class="sxs-lookup"><span data-stu-id="afba3-615">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="afba3-616">`Newtonsoft.Json`でサポートされていないの機能が必要な場合は `System.Text.Json` 、の[[Newtonsoft.Jsへの切り替え](xref:migration/22-to-30#switch-to-newtonsoftjson)] を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-616">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="afba3-617">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-617">MessagePack serialization options</span></span>

<span data-ttu-id="afba3-618">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-618">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="afba3-619">詳細については[、「Messagepack」を :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-619">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-620">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-620">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="afba3-621">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-621">Configure server options</span></span>

<span data-ttu-id="afba3-622">次の表では、ハブを構成するためのオプションについて説明し :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-622">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="afba3-623">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-623">Option</span></span> | <span data-ttu-id="afba3-624">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-624">Default Value</span></span> | <span data-ttu-id="afba3-625">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-625">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="afba3-626">30 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-626">30 seconds</span></span> | <span data-ttu-id="afba3-627">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-627">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="afba3-628">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="afba3-628">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="afba3-629">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-629">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="afba3-630">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-630">15 seconds</span></span> | <span data-ttu-id="afba3-631">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="afba3-631">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="afba3-632">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-632">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-633">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-633">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-634">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-634">15 seconds</span></span> | <span data-ttu-id="afba3-635">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-635">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="afba3-636">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-636">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="afba3-637">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-637">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="afba3-638">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="afba3-638">All installed protocols</span></span> | <span data-ttu-id="afba3-639">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="afba3-639">Protocols supported by this hub.</span></span> <span data-ttu-id="afba3-640">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="afba3-640">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="afba3-641">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-641">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="afba3-642">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="afba3-642">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="afba3-643">クライアントアップロードストリームに対してバッファーできる項目の最大数。</span><span class="sxs-lookup"><span data-stu-id="afba3-643">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="afba3-644">この制限に達すると、サーバーがストリーム項目を処理するまで、呼び出しの処理はブロックされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-644">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="afba3-645">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-645">32 KB</span></span> | <span data-ttu-id="afba3-646">1つの受信ハブメッセージの最大サイズ。</span><span class="sxs-lookup"><span data-stu-id="afba3-646">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="afba3-647">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-647">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="afba3-648">1つのハブのオプションは、で提供されるグローバルオプションを上書き `Add:::no-loc(SignalR):::` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-648">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="afba3-649">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-649">Advanced HTTP configuration options</span></span>

<span data-ttu-id="afba3-650">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="afba3-650">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="afba3-651">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-651">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="afba3-652">次の表では、ASP.NET Core :::no-loc(SignalR)::: の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="afba3-652">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="afba3-653">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-653">Option</span></span> | <span data-ttu-id="afba3-654">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-654">Default Value</span></span> | <span data-ttu-id="afba3-655">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-655">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="afba3-656">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-656">32 KB</span></span> | <span data-ttu-id="afba3-657">サーバーがバック圧力を適用する前に、クライアントから受信した最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-657">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="afba3-658">この値を大きくすると、バック圧力を適用せずに、より大きいメッセージをサーバーがより迅速に受信できるようになりますが、メモリ使用量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-658">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="afba3-659">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="afba3-659">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="afba3-660">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="afba3-660">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="afba3-661">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-661">32 KB</span></span> | <span data-ttu-id="afba3-662">サーバーがバック圧力を観察する前に、アプリによって送信された最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-662">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="afba3-663">この値を大きくすると、バック圧力を待機することなく、より大きなメッセージをサーバーでバッファーできるようになりますが、メモリの消費量が増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-663">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="afba3-664">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="afba3-664">All Transports are enabled.</span></span> | <span data-ttu-id="afba3-665">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="afba3-665">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="afba3-666">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-666">See below.</span></span> | <span data-ttu-id="afba3-667">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-667">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="afba3-668">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-668">See below.</span></span> | <span data-ttu-id="afba3-669">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-669">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="afba3-670">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-670">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="afba3-671">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-671">Option</span></span> | <span data-ttu-id="afba3-672">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-672">Default Value</span></span> | <span data-ttu-id="afba3-673">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-673">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="afba3-674">90秒</span><span class="sxs-lookup"><span data-stu-id="afba3-674">90 seconds</span></span> | <span data-ttu-id="afba3-675">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-675">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="afba3-676">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="afba3-676">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="afba3-677">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-677">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="afba3-678">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-678">Option</span></span> | <span data-ttu-id="afba3-679">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-679">Default Value</span></span> | <span data-ttu-id="afba3-680">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="afba3-681">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-681">5 seconds</span></span> | <span data-ttu-id="afba3-682">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="afba3-682">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="afba3-683">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-683">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="afba3-684">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="afba3-684">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="afba3-685">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-685">Configure client options</span></span>

<span data-ttu-id="afba3-686">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-686">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="afba3-687">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-687">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="afba3-688">ログの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-688">Configure logging</span></span>

<span data-ttu-id="afba3-689">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-689">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="afba3-690">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-690">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="afba3-691">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-691">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-692">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-692">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="afba3-693">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-693">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="afba3-694">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-694">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="afba3-695">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afba3-695">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="afba3-696">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-696">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="afba3-697">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-697">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="afba3-698">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="afba3-698">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="afba3-699">値の代わり `LogLevel` に、ログレベル名を表す値を指定することもでき `string` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-699">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="afba3-700">これは :::no-loc(SignalR)::: 、定数にアクセスできない環境でログを構成する場合に便利です `LogLevel` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-700">This is useful when configuring :::no-loc(SignalR)::: logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="afba3-701">次の表は、使用可能なログレベルを示しています。</span><span class="sxs-lookup"><span data-stu-id="afba3-701">The following table lists the available log levels.</span></span> <span data-ttu-id="afba3-702">`configureLogging`ログに記録される **最小** ログレベルを設定するために指定する値。</span><span class="sxs-lookup"><span data-stu-id="afba3-702">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="afba3-703">このレベルでログに記録されたメッセージ、 **またはテーブルに記載されているレベルの** メッセージはログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-703">Messages logged at this level, **or the levels listed after it in the table** , will be logged.</span></span>

| <span data-ttu-id="afba3-704">String</span><span class="sxs-lookup"><span data-stu-id="afba3-704">String</span></span>                      | <span data-ttu-id="afba3-705">LogLevel</span><span class="sxs-lookup"><span data-stu-id="afba3-705">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="afba3-706">`info`**または**`information`</span><span class="sxs-lookup"><span data-stu-id="afba3-706">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="afba3-707">`warn`**または**`warning`</span><span class="sxs-lookup"><span data-stu-id="afba3-707">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="afba3-708">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-708">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="afba3-709">ログ記録の詳細については、 [ :::no-loc(SignalR)::: 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-709">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="afba3-710">:::no-loc(SignalR):::Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="afba3-710">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="afba3-711">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="afba3-711">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="afba3-712">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` :::no-loc(SignalR)::: います。</span><span class="sxs-lookup"><span data-stu-id="afba3-712">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="afba3-713">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="afba3-713">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="afba3-714">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="afba3-714">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="afba3-715">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-715">Configure allowed transports</span></span>

<span data-ttu-id="afba3-716">によって使用されるトランスポートは、 :::no-loc(SignalR)::: `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-716">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="afba3-717">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-717">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="afba3-718">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="afba3-718">All transports are enabled by default.</span></span>

<span data-ttu-id="afba3-719">たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="afba3-719">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="afba3-720">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-720">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="afba3-721">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="afba3-721">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="afba3-722">Java クライアントでは、でメソッドを使用してトランスポートを選択し `withTransport` `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-722">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="afba3-723">Java クライアントでは、既定で Websocket トランスポートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-723">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="afba3-724">Java クライアントでは、トランスポートフォールバックはまだサポートされてい :::no-loc(SignalR)::: ません。</span><span class="sxs-lookup"><span data-stu-id="afba3-724">The :::no-loc(SignalR)::: Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="afba3-725">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-725">Configure bearer authentication</span></span>

<span data-ttu-id="afba3-726">要求と共に認証データを提供するには :::no-loc(SignalR)::: 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-726">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="afba3-727">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="afba3-727">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="afba3-728">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-728">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="afba3-729">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-729">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="afba3-730">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-730">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="afba3-731">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-731">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="afba3-732">Java クライアントでは、 :::no-loc(SignalR)::: [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-732">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="afba3-733">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-733">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="afba3-734">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-734">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="afba3-735">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-735">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="afba3-736">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-736">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-737">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-737">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-738">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-738">Option</span></span> | <span data-ttu-id="afba3-739">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-739">Default value</span></span> | <span data-ttu-id="afba3-740">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-740">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="afba3-741">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-742">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-742">Timeout for server activity.</span></span> <span data-ttu-id="afba3-743">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-744">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-745">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="afba3-746">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-746">15 seconds</span></span> | <span data-ttu-id="afba3-747">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-748">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-749">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-750">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-750">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-751">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-751">15 seconds</span></span> | <span data-ttu-id="afba3-752">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-752">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-753">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-753">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-754">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-754">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="afba3-755">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-755">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="afba3-756">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-756">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-757">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-757">Option</span></span> | <span data-ttu-id="afba3-758">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-758">Default value</span></span> | <span data-ttu-id="afba3-759">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-759">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="afba3-760">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-760">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-761">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-761">Timeout for server activity.</span></span> <span data-ttu-id="afba3-762">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-762">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="afba3-763">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-763">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-764">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-764">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="afba3-765">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-765">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="afba3-766">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-766">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-767">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-767">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-768">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-768">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-769">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-769">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-770">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-770">Option</span></span> | <span data-ttu-id="afba3-771">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-771">Default value</span></span> | <span data-ttu-id="afba3-772">説明</span><span class="sxs-lookup"><span data-stu-id="afba3-772">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="afba3-773">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="afba3-773">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="afba3-774">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-774">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-775">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-775">Timeout for server activity.</span></span> <span data-ttu-id="afba3-776">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-776">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-777">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-777">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-778">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-778">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="afba3-779">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-779">15 seconds</span></span> | <span data-ttu-id="afba3-780">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-780">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-781">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-781">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-782">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-782">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-783">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-783">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="afba3-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="afba3-784">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="afba3-785">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-785">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="afba3-786">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-786">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-787">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-787">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-788">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-788">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="afba3-789">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-789">Configure additional options</span></span>

<span data-ttu-id="afba3-790">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-790">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-791">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-791">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-792">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-792">.NET Option</span></span> |  <span data-ttu-id="afba3-793">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-793">Default value</span></span> | <span data-ttu-id="afba3-794">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-794">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="afba3-795">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-795">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="afba3-796">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-796">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-797">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-797">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-798">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-798">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="afba3-799">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-799">Empty</span></span> | <span data-ttu-id="afba3-800">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-800">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="afba3-801">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-801">Empty</span></span> | <span data-ttu-id="afba3-802">:::no-loc(cookie):::すべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-802">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="afba3-803">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-803">Empty</span></span> | <span data-ttu-id="afba3-804">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="afba3-804">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="afba3-805">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-805">5 seconds</span></span> | <span data-ttu-id="afba3-806">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="afba3-806">WebSockets only.</span></span> <span data-ttu-id="afba3-807">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-807">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="afba3-808">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-808">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="afba3-809">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-809">Empty</span></span> | <span data-ttu-id="afba3-810">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-810">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="afba3-811">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-811">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="afba3-812">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="afba3-812">Not used for WebSocket connections.</span></span> <span data-ttu-id="afba3-813">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-813">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="afba3-814">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-814">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="afba3-815">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( :::no-loc(Cookie)::: s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="afba3-815">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="afba3-816">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="afba3-816">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="afba3-817">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-817">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="afba3-818">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="afba3-818">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="afba3-819">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-819">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="afba3-820">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-820">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="afba3-821">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-821">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-822">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-822">JavaScript Option</span></span> | <span data-ttu-id="afba3-823">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-823">Default Value</span></span> | <span data-ttu-id="afba3-824">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-824">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="afba3-825">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="afba3-826"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-826">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="afba3-827">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-827">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="afba3-828">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-828">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-829">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-829">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-830">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-830">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-831">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-831">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-832">Java オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-832">Java Option</span></span> | <span data-ttu-id="afba3-833">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-833">Default Value</span></span> | <span data-ttu-id="afba3-834">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-834">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="afba3-835">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-835">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="afba3-836">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-836">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-837">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-837">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-838">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-838">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="afba3-839">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="afba3-839">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="afba3-840">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-840">Empty</span></span> | <span data-ttu-id="afba3-841">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-841">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="afba3-842">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-842">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="afba3-843">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-843">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="afba3-844">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="afba3-844">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="afba3-845">その他の資料</span><span class="sxs-lookup"><span data-stu-id="afba3-845">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="afba3-846">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-846">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="afba3-847">ASP.NET Core は :::no-loc(SignalR)::: 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="afba3-847">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="afba3-848">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="afba3-848">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="afba3-849">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドに[Add を :::no-loc(SignalR)::: 追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)した後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-849">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="afba3-850">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-850">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="afba3-851">そのオブジェクトの [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="afba3-851">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="afba3-852">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-852">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="afba3-853">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-853">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="afba3-854">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-854">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="afba3-855">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-855">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="afba3-856">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-856">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="afba3-857">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-857">MessagePack serialization options</span></span>

<span data-ttu-id="afba3-858">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-858">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="afba3-859">詳細については[、「Messagepack」を :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-859">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-860">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-860">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="afba3-861">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-861">Configure server options</span></span>

<span data-ttu-id="afba3-862">次の表では、ハブを構成するためのオプションについて説明し :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-862">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="afba3-863">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-863">Option</span></span> | <span data-ttu-id="afba3-864">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-864">Default Value</span></span> | <span data-ttu-id="afba3-865">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-865">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="afba3-866">30 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-866">30 seconds</span></span> | <span data-ttu-id="afba3-867">サーバーは、この間隔で (キープアライブを含む) メッセージを受信していない場合に、クライアントが切断されていると見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-867">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="afba3-868">クライアントが実際に切断されているとマークされるまでに、このタイムアウト間隔より長くかかることがあります。これは、この実装方法によるものです。</span><span class="sxs-lookup"><span data-stu-id="afba3-868">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="afba3-869">推奨値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-869">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="afba3-870">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-870">15 seconds</span></span> | <span data-ttu-id="afba3-871">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="afba3-871">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="afba3-872">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-872">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-873">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-873">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-874">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-874">15 seconds</span></span> | <span data-ttu-id="afba3-875">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-875">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="afba3-876">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-876">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="afba3-877">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-877">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="afba3-878">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="afba3-878">All installed protocols</span></span> | <span data-ttu-id="afba3-879">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="afba3-879">Protocols supported by this hub.</span></span> <span data-ttu-id="afba3-880">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="afba3-880">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="afba3-881">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-881">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="afba3-882">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="afba3-882">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="afba3-883">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-883">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="afba3-884">1つのハブのオプションは、で提供されるグローバルオプションを上書き `Add:::no-loc(SignalR):::` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-884">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="afba3-885">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-885">Advanced HTTP configuration options</span></span>

<span data-ttu-id="afba3-886">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="afba3-886">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="afba3-887">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-887">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="afba3-888">次の表では、ASP.NET Core :::no-loc(SignalR)::: の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="afba3-888">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="afba3-889">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-889">Option</span></span> | <span data-ttu-id="afba3-890">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-890">Default Value</span></span> | <span data-ttu-id="afba3-891">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-891">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="afba3-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-892">32 KB</span></span> | <span data-ttu-id="afba3-893">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-893">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="afba3-894">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-894">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="afba3-895">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="afba3-895">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="afba3-896">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="afba3-896">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="afba3-897">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-897">32 KB</span></span> | <span data-ttu-id="afba3-898">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-898">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="afba3-899">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-899">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="afba3-900">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="afba3-900">All Transports are enabled.</span></span> | <span data-ttu-id="afba3-901">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="afba3-901">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="afba3-902">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-902">See below.</span></span> | <span data-ttu-id="afba3-903">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-903">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="afba3-904">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-904">See below.</span></span> | <span data-ttu-id="afba3-905">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-905">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="afba3-906">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-906">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="afba3-907">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-907">Option</span></span> | <span data-ttu-id="afba3-908">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-908">Default Value</span></span> | <span data-ttu-id="afba3-909">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-909">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="afba3-910">90秒</span><span class="sxs-lookup"><span data-stu-id="afba3-910">90 seconds</span></span> | <span data-ttu-id="afba3-911">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-911">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="afba3-912">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="afba3-912">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="afba3-913">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-913">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="afba3-914">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-914">Option</span></span> | <span data-ttu-id="afba3-915">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-915">Default Value</span></span> | <span data-ttu-id="afba3-916">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-916">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="afba3-917">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-917">5 seconds</span></span> | <span data-ttu-id="afba3-918">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="afba3-918">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="afba3-919">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-919">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="afba3-920">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="afba3-920">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="afba3-921">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-921">Configure client options</span></span>

<span data-ttu-id="afba3-922">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-922">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="afba3-923">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-923">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="afba3-924">ログの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-924">Configure logging</span></span>

<span data-ttu-id="afba3-925">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-925">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="afba3-926">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-926">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="afba3-927">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-927">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-928">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-928">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="afba3-929">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-929">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="afba3-930">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-930">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="afba3-931">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afba3-931">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="afba3-932">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-932">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="afba3-933">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-933">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="afba3-934">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="afba3-934">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="afba3-935">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-935">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="afba3-936">ログ記録の詳細については、 [ :::no-loc(SignalR)::: 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-936">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="afba3-937">:::no-loc(SignalR):::Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="afba3-937">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="afba3-938">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="afba3-938">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="afba3-939">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` :::no-loc(SignalR)::: います。</span><span class="sxs-lookup"><span data-stu-id="afba3-939">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="afba3-940">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="afba3-940">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="afba3-941">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="afba3-941">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="afba3-942">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-942">Configure allowed transports</span></span>

<span data-ttu-id="afba3-943">によって使用されるトランスポートは、 :::no-loc(SignalR)::: `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-943">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="afba3-944">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-944">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="afba3-945">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="afba3-945">All transports are enabled by default.</span></span>

<span data-ttu-id="afba3-946">たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="afba3-946">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="afba3-947">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-947">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="afba3-948">このバージョンの Java クライアント websocket は、唯一の利用可能なトランスポートです。</span><span class="sxs-lookup"><span data-stu-id="afba3-948">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="afba3-949">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-949">Configure bearer authentication</span></span>

<span data-ttu-id="afba3-950">要求と共に認証データを提供するには :::no-loc(SignalR)::: 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-950">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="afba3-951">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="afba3-951">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="afba3-952">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-952">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="afba3-953">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-953">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="afba3-954">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-954">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="afba3-955">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-955">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="afba3-956">Java クライアントでは、 :::no-loc(SignalR)::: [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-956">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="afba3-957">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-957">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="afba3-958">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-958">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="afba3-959">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-959">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="afba3-960">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-960">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-961">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-961">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-962">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-962">Option</span></span> | <span data-ttu-id="afba3-963">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-963">Default value</span></span> | <span data-ttu-id="afba3-964">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-964">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="afba3-965">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-965">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-966">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-966">Timeout for server activity.</span></span> <span data-ttu-id="afba3-967">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-967">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-968">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-968">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-969">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-969">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="afba3-970">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-970">15 seconds</span></span> | <span data-ttu-id="afba3-971">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-971">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-972">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-972">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-973">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-973">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-974">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-974">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-975">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-975">15 seconds</span></span> | <span data-ttu-id="afba3-976">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-976">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-977">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-977">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-978">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-978">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="afba3-979">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-979">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="afba3-980">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-980">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-981">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-981">Option</span></span> | <span data-ttu-id="afba3-982">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-982">Default value</span></span> | <span data-ttu-id="afba3-983">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-983">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="afba3-984">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-984">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-985">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-985">Timeout for server activity.</span></span> <span data-ttu-id="afba3-986">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-986">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="afba3-987">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-987">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-988">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-988">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="afba3-989">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-989">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="afba3-990">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-990">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-991">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-991">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-992">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-992">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-993">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-993">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-994">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-994">Option</span></span> | <span data-ttu-id="afba3-995">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-995">Default value</span></span> | <span data-ttu-id="afba3-996">説明</span><span class="sxs-lookup"><span data-stu-id="afba3-996">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="afba3-997">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="afba3-997">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="afba3-998">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-998">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-999">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-999">Timeout for server activity.</span></span> <span data-ttu-id="afba3-1000">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1000">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-1001">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1001">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-1002">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1002">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="afba3-1003">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1003">15 seconds</span></span> | <span data-ttu-id="afba3-1004">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-1004">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-1005">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1005">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-1006">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1006">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-1007">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1007">For more detail on the Handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="afba3-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="afba3-1008">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="afba3-1009">15秒 (15000 ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-1009">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="afba3-1010">クライアントが ping メッセージを送信する間隔を決定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1010">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="afba3-1011">クライアントからメッセージを送信すると、タイマーが間隔の開始日にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1011">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="afba3-1012">クライアントがサーバー上のセットにメッセージを送信していない場合、 `ClientTimeoutInterval` サーバーはクライアントを切断したと見なします。</span><span class="sxs-lookup"><span data-stu-id="afba3-1012">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="afba3-1013">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-1013">Configure additional options</span></span>

<span data-ttu-id="afba3-1014">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1014">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-1015">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-1015">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-1016">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1016">.NET Option</span></span> |  <span data-ttu-id="afba3-1017">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1017">Default value</span></span> | <span data-ttu-id="afba3-1018">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1018">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="afba3-1019">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-1019">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="afba3-1020">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1020">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-1021">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1021">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-1022">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-1022">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="afba3-1023">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1023">Empty</span></span> | <span data-ttu-id="afba3-1024">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-1024">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="afba3-1025">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1025">Empty</span></span> | <span data-ttu-id="afba3-1026">:::no-loc(cookie):::すべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-1026">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="afba3-1027">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1027">Empty</span></span> | <span data-ttu-id="afba3-1028">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="afba3-1028">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="afba3-1029">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1029">5 seconds</span></span> | <span data-ttu-id="afba3-1030">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1030">WebSockets only.</span></span> <span data-ttu-id="afba3-1031">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-1031">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="afba3-1032">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1032">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="afba3-1033">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1033">Empty</span></span> | <span data-ttu-id="afba3-1034">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1034">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="afba3-1035">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-1035">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="afba3-1036">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="afba3-1036">Not used for WebSocket connections.</span></span> <span data-ttu-id="afba3-1037">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1037">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="afba3-1038">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1038">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="afba3-1039">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( :::no-loc(Cookie)::: s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="afba3-1039">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="afba3-1040">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1040">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="afba3-1041">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1041">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="afba3-1042">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1042">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="afba3-1043">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-1043">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="afba3-1044">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1044">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="afba3-1045">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-1045">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-1046">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1046">JavaScript Option</span></span> | <span data-ttu-id="afba3-1047">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1047">Default Value</span></span> | <span data-ttu-id="afba3-1048">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1048">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="afba3-1049">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-1049">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="afba3-1050"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1050">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="afba3-1051">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1051">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="afba3-1052">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1052">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-1053">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1053">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-1054">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-1054">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-1055">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-1055">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-1056">Java オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1056">Java Option</span></span> | <span data-ttu-id="afba3-1057">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1057">Default Value</span></span> | <span data-ttu-id="afba3-1058">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1058">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="afba3-1059">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-1059">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="afba3-1060">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1060">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-1061">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1061">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-1062">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-1062">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="afba3-1063">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="afba3-1063">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="afba3-1064">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1064">Empty</span></span> | <span data-ttu-id="afba3-1065">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1065">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="afba3-1066">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1066">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="afba3-1067">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1067">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="afba3-1068">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="afba3-1068">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="afba3-1069">その他の資料</span><span class="sxs-lookup"><span data-stu-id="afba3-1069">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="afba3-1070">JSON/MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1070">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="afba3-1071">ASP.NET Core は :::no-loc(SignalR)::: 、 [JSON](https://www.json.org/) と [messagepack](https://msgpack.org/index.html)の2つのプロトコルをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="afba3-1071">ASP.NET Core :::no-loc(SignalR)::: supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="afba3-1072">各プロトコルには、シリアル化の構成オプションがあります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1072">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="afba3-1073">JSON のシリアル化は、 [Addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)拡張メソッドを使用してサーバー上で構成できます。これは、メソッドに[Add を :::no-loc(SignalR)::: 追加](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr)した後に追加でき `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1073">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="afba3-1074">メソッドは、 `AddJsonProtocol` オブジェクトを受け取るデリゲートを受け取り `options` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1074">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="afba3-1075">そのオブジェクトの [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) プロパティは、 `JsonSerializerSettings` 引数と戻り値のシリアル化を構成するために使用できる JSON.NET オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="afba3-1075">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="afba3-1076">詳細については、[JSON.NET のドキュメント](https://www.newtonsoft.com/json/help/html/Introduction.htm)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1076">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="afba3-1077">たとえば、"キャメルケース" という既定の名前を使用するのではなく、"" という名前のプロパティ名を使用するようにシリアライザーを構成するには、で次のコードを使用し `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1077">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.Add:::no-loc(SignalR):::()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="afba3-1078">.NET クライアントでは、 `AddJsonProtocol` [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)に同じ拡張メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1078">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="afba3-1079">`Microsoft.Extensions.DependencyInjection`拡張メソッドを解決するには、名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1079">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="afba3-1080">現時点では、JSON シリアル化を JavaScript クライアントで構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-1080">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="afba3-1081">MessagePack のシリアル化オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1081">MessagePack serialization options</span></span>

<span data-ttu-id="afba3-1082">MessagePack のシリアル化は、 [Addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) 呼び出しにデリゲートを指定することによって構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1082">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="afba3-1083">詳細については[、「Messagepack」を :::no-loc(SignalR)::: ](xref:signalr/messagepackhubprotocol)参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1083">See [MessagePack in :::no-loc(SignalR):::](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-1084">現時点では、JavaScript クライアントで MessagePack のシリアル化を構成することはできません。</span><span class="sxs-lookup"><span data-stu-id="afba3-1084">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="afba3-1085">サーバーオプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-1085">Configure server options</span></span>

<span data-ttu-id="afba3-1086">次の表では、ハブを構成するためのオプションについて説明し :::no-loc(SignalR)::: ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1086">The following table describes options for configuring :::no-loc(SignalR)::: hubs:</span></span>

| <span data-ttu-id="afba3-1087">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1087">Option</span></span> | <span data-ttu-id="afba3-1088">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1088">Default Value</span></span> | <span data-ttu-id="afba3-1089">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1089">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="afba3-1090">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1090">15 seconds</span></span> | <span data-ttu-id="afba3-1091">この期間内にクライアントが初期ハンドシェイクメッセージを送信しない場合、接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1091">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="afba3-1092">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1092">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-1093">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1093">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="afba3-1094">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1094">15 seconds</span></span> | <span data-ttu-id="afba3-1095">サーバーがこの間隔内にメッセージを送信していない場合は、接続を開いたままにするために ping メッセージが自動的に送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1095">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="afba3-1096">変更する場合は、 `KeepAliveInterval` クライアントの設定を変更し `ServerTimeout` / `serverTimeoutInMilliseconds` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1096">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="afba3-1097">推奨 `ServerTimeout` / `serverTimeoutInMilliseconds` 値は、値の倍精度浮動小数点数です `KeepAliveInterval` 。</span><span class="sxs-lookup"><span data-stu-id="afba3-1097">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="afba3-1098">インストールされているすべてのプロトコル</span><span class="sxs-lookup"><span data-stu-id="afba3-1098">All installed protocols</span></span> | <span data-ttu-id="afba3-1099">このハブでサポートされているプロトコル。</span><span class="sxs-lookup"><span data-stu-id="afba3-1099">Protocols supported by this hub.</span></span> <span data-ttu-id="afba3-1100">既定では、サーバーに登録されているすべてのプロトコルが許可されますが、個々のハブの特定のプロトコルを無効にするために、この一覧からプロトコルを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1100">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="afba3-1101">`true`の場合、ハブメソッドで例外がスローされると、詳細な例外メッセージがクライアントに返されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1101">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="afba3-1102">既定値はです `false` 。これらの例外メッセージには機密情報が含まれる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="afba3-1102">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="afba3-1103">オプションは、の呼び出しにオプションデリゲートを指定することで、すべてのハブに対して構成でき `Add:::no-loc(SignalR):::` `Startup.ConfigureServices` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1103">Options can be configured for all hubs by providing an options delegate to the `Add:::no-loc(SignalR):::` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="afba3-1104">1つのハブのオプションは、で提供されるグローバルオプションを上書き `Add:::no-loc(SignalR):::` します。また、次のものを使用して構成でき <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*> ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1104">Options for a single hub override the global options provided in `Add:::no-loc(SignalR):::` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(SignalR):::DependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.Add:::no-loc(SignalR):::().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="afba3-1105">詳細な HTTP 構成オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1105">Advanced HTTP configuration options</span></span>

<span data-ttu-id="afba3-1106">`HttpConnectionDispatcherOptions`トランスポートおよびメモリバッファー管理に関連する詳細設定を構成するには、を使用します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1106">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="afba3-1107">これらのオプションは、で[Maphub \<T> ](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub)にデリゲートを渡すことによって構成され `Startup.Configure` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1107">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.Use:::no-loc(SignalR):::((configure) =>
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

<span data-ttu-id="afba3-1108">次の表では、ASP.NET Core :::no-loc(SignalR)::: の詳細な HTTP オプションを構成するためのオプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1108">The following table describes options for configuring ASP.NET Core :::no-loc(SignalR):::'s advanced HTTP options:</span></span>

| <span data-ttu-id="afba3-1109">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1109">Option</span></span> | <span data-ttu-id="afba3-1110">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1110">Default Value</span></span> | <span data-ttu-id="afba3-1111">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1111">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="afba3-1112">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-1112">32 KB</span></span> | <span data-ttu-id="afba3-1113">クライアントから受信した、サーバーがバッファーする最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-1113">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="afba3-1114">この値を大きくすると、サーバーはより大きなメッセージを受け取れますが、メモリの消費に悪影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1114">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="afba3-1115">`Authorize`ハブクラスに適用された属性から自動的に収集されるデータ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1115">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="afba3-1116">クライアントがハブへの接続を承認されているかどうかを判断するために使用される [Iauthorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) オブジェクトの一覧。</span><span class="sxs-lookup"><span data-stu-id="afba3-1116">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="afba3-1117">32 KB</span><span class="sxs-lookup"><span data-stu-id="afba3-1117">32 KB</span></span> | <span data-ttu-id="afba3-1118">サーバーがバッファーするアプリによって送信される最大バイト数。</span><span class="sxs-lookup"><span data-stu-id="afba3-1118">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="afba3-1119">この値を大きくすると、サーバーはより大きなメッセージを送信できるようになりますが、メモリの消費に悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1119">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="afba3-1120">すべてのトランスポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1120">All Transports are enabled.</span></span> | <span data-ttu-id="afba3-1121">`HttpTransportType`クライアントが接続に使用できるトランスポートを制限できる、値のビットフラグ列挙値。</span><span class="sxs-lookup"><span data-stu-id="afba3-1121">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="afba3-1122">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1122">See below.</span></span> | <span data-ttu-id="afba3-1123">長いポーリングトランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-1123">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="afba3-1124">次を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1124">See below.</span></span> | <span data-ttu-id="afba3-1125">Websocket トランスポートに固有の追加オプション。</span><span class="sxs-lookup"><span data-stu-id="afba3-1125">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="afba3-1126">長いポーリングトランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `LongPolling` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1126">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="afba3-1127">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1127">Option</span></span> | <span data-ttu-id="afba3-1128">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1128">Default Value</span></span> | <span data-ttu-id="afba3-1129">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1129">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="afba3-1130">90秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1130">90 seconds</span></span> | <span data-ttu-id="afba3-1131">1回のポーリング要求を終了する前に、サーバーがクライアントへのメッセージ送信を待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-1131">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="afba3-1132">この値を小さくすると、クライアントは新しいポーリング要求をより頻繁に発行します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1132">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="afba3-1133">WebSocket トランスポートには、次のプロパティを使用して構成できる追加のオプションがあり `WebSockets` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1133">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="afba3-1134">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1134">Option</span></span> | <span data-ttu-id="afba3-1135">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1135">Default Value</span></span> | <span data-ttu-id="afba3-1136">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1136">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="afba3-1137">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1137">5 seconds</span></span> | <span data-ttu-id="afba3-1138">サーバーが閉じられた後、この期間内にクライアントが閉じるのに失敗した場合、接続は終了します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1138">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="afba3-1139">`Sec-WebSocket-Protocol`ヘッダーをカスタム値に設定するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-1139">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="afba3-1140">デリゲートは、クライアントから要求された値を入力として受け取り、目的の値を返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="afba3-1140">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="afba3-1141">クライアント オプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-1141">Configure client options</span></span>

<span data-ttu-id="afba3-1142">クライアントオプションは、 `HubConnectionBuilder` (.net および JavaScript クライアントで使用可能な) 型で構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1142">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="afba3-1143">Java クライアントでも使用できますが、 `HttpHubConnectionBuilder` サブクラスは、ビルダーの構成オプションとそれ自体に含まれてい `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1143">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="afba3-1144">ログの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-1144">Configure logging</span></span>

<span data-ttu-id="afba3-1145">ログは、.NET クライアントでメソッドを使用して構成され `ConfigureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1145">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="afba3-1146">ログプロバイダーとフィルターは、サーバーと同じ方法で登録できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1146">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="afba3-1147">詳細については、ASP.NET Core のドキュメントの [ログ](xref:fundamentals/logging/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1147">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="afba3-1148">ログプロバイダーを登録するには、必要なパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1148">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="afba3-1149">完全な一覧については、ドキュメントの「 [組み込みのログプロバイダー](xref:fundamentals/logging/index#built-in-logging-providers) 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1149">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="afba3-1150">たとえば、コンソールのログ記録を有効にするには、NuGet パッケージをインストールし `Microsoft.Extensions.Logging.Console` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1150">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="afba3-1151">`AddConsole`拡張メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1151">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="afba3-1152">JavaScript クライアントでは、同様の `configureLogging` メソッドが存在します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1152">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="afba3-1153">`LogLevel`生成するログメッセージの最小レベルを示す値を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1153">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="afba3-1154">ログは、ブラウザーのコンソールウィンドウに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1154">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="afba3-1155">完全にログ記録を無効にするには、メソッドでを指定し `signalR.LogLevel.None` `configureLogging` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1155">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="afba3-1156">ログ記録の詳細については、 [ :::no-loc(SignalR)::: 診断のドキュメント](xref:signalr/diagnostics)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1156">For more information on logging, see the [:::no-loc(SignalR)::: Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="afba3-1157">:::no-loc(SignalR):::Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1157">The :::no-loc(SignalR)::: Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="afba3-1158">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1158">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="afba3-1159">次のコードスニペットは、Java クライアントでを使用する方法を示して `java.util.logging` :::no-loc(SignalR)::: います。</span><span class="sxs-lookup"><span data-stu-id="afba3-1159">The following code snippet shows how to use `java.util.logging` with the :::no-loc(SignalR)::: Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="afba3-1160">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1160">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="afba3-1161">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="afba3-1161">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="afba3-1162">許可されたトランスポートの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-1162">Configure allowed transports</span></span>

<span data-ttu-id="afba3-1163">によって使用されるトランスポートは、 :::no-loc(SignalR)::: `WithUrl` (JavaScript では) の呼び出しで構成でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1163">The transports used by :::no-loc(SignalR)::: can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="afba3-1164">の値のビットごとの OR を使用して、 `HttpTransportType` 指定したトランスポートのみを使用するようにクライアントを制限できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1164">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="afba3-1165">既定では、すべてのトランスポートが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="afba3-1165">All transports are enabled by default.</span></span>

<span data-ttu-id="afba3-1166">たとえば、Server-Sent イベントの転送を無効にし、Websocket と長いポーリング接続を許可するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="afba3-1166">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="afba3-1167">JavaScript クライアントでは、 `transport` 次のように指定されたオプションオブジェクトのフィールドを設定することによって、トランスポートを構成し `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1167">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="afba3-1168">ベアラー認証を構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-1168">Configure bearer authentication</span></span>

<span data-ttu-id="afba3-1169">要求と共に認証データを提供するには :::no-loc(SignalR)::: 、 `AccessTokenProvider` (JavaScript の) オプションを使用して、目的の `accessTokenFactory` アクセストークンを返す関数を指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1169">To provide authentication data along with :::no-loc(SignalR)::: requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="afba3-1170">.NET クライアントでは、このアクセストークンは HTTP "ベアラー認証" トークンとして渡されます ( `Authorization` の型のヘッダーを使用し `Bearer` ます)。</span><span class="sxs-lookup"><span data-stu-id="afba3-1170">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="afba3-1171">JavaScript クライアントでは、アクセストークンはベアラートークンとして使用されますが、ブラウザー Api がヘッダー (具体的には Server-Sent イベントと Websocket 要求) を適用する機能を制限する場合を **除き** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1171">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="afba3-1172">このような場合、アクセストークンはクエリ文字列値として指定され `access_token` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1172">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="afba3-1173">.NET クライアントでは、 `AccessTokenProvider` のオプションデリゲートを使用してオプションを指定でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1173">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="afba3-1174">JavaScript クライアントでは、の options オブジェクトのフィールドを設定することにより、アクセストークンが構成され `accessTokenFactory` `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1174">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="afba3-1175">Java クライアントでは、 :::no-loc(SignalR)::: [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)にアクセストークンファクトリを提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1175">In the :::no-loc(SignalR)::: Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="afba3-1176">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)を[1 つ \<String> だけ](https://reactivex.io/documentation/single.html)指定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1176">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="afba3-1177">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1177">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="afba3-1178">タイムアウトとキープアライブオプションを構成する</span><span class="sxs-lookup"><span data-stu-id="afba3-1178">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="afba3-1179">タイムアウトとキープアライブの動作を構成するための追加のオプションは、オブジェクト自体で利用でき `HubConnection` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1179">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-1180">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-1180">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-1181">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1181">Option</span></span> | <span data-ttu-id="afba3-1182">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1182">Default value</span></span> | <span data-ttu-id="afba3-1183">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1183">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="afba3-1184">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-1184">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-1185">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-1185">Timeout for server activity.</span></span> <span data-ttu-id="afba3-1186">サーバーがこの間隔でメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1186">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-1187">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1187">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-1188">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1188">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="afba3-1189">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1189">15 seconds</span></span> | <span data-ttu-id="afba3-1190">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-1190">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-1191">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、 `Closed` (JavaScript で) イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1191">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="afba3-1192">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1192">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-1193">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1193">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="afba3-1194">.NET クライアントでは、タイムアウト値は値として指定され `TimeSpan` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1194">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="afba3-1195">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-1195">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-1196">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1196">Option</span></span> | <span data-ttu-id="afba3-1197">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1197">Default value</span></span> | <span data-ttu-id="afba3-1198">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1198">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="afba3-1199">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-1199">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-1200">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-1200">Timeout for server activity.</span></span> <span data-ttu-id="afba3-1201">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onclose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1201">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="afba3-1202">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1202">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-1203">推奨値は、 `KeepAliveInterval` ping が到着するまでの時間を考慮して、サーバーの値の少なくとも2倍の値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1203">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-1204">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-1204">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-1205">オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1205">Option</span></span> | <span data-ttu-id="afba3-1206">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1206">Default value</span></span> | <span data-ttu-id="afba3-1207">説明</span><span class="sxs-lookup"><span data-stu-id="afba3-1207">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="afba3-1208">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="afba3-1208">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="afba3-1209">30秒 (3万ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="afba3-1209">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="afba3-1210">サーバーの利用状況のタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-1210">Timeout for server activity.</span></span> <span data-ttu-id="afba3-1211">サーバーがこの間隔内にメッセージを送信しなかった場合、クライアントはサーバーを切断したと見なし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1211">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-1212">この値は、ping メッセージをサーバーから送信 **し** 、タイムアウト間隔内にクライアントが受信するのに十分な大きさである必要があります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1212">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="afba3-1213">推奨値は、サーバーの値の少なくとも2倍の数で、 `KeepAliveInterval` ping が到着するまでの時間を確保します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1213">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="afba3-1214">15 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1214">15 seconds</span></span> | <span data-ttu-id="afba3-1215">初期サーバーハンドシェイクのタイムアウト。</span><span class="sxs-lookup"><span data-stu-id="afba3-1215">Timeout for initial server handshake.</span></span> <span data-ttu-id="afba3-1216">サーバーがこの間隔でハンドシェイク応答を送信しない場合、クライアントはハンドシェイクをキャンセルし、イベントをトリガーし `onClose` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1216">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="afba3-1217">これは、ネットワーク待ち時間が非常に長いためにハンドシェイクのタイムアウトエラーが発生している場合にのみ変更する必要がある詳細設定です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1217">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="afba3-1218">ハンドシェイクプロセスの詳細については、「 [ :::no-loc(SignalR)::: Hub プロトコルの仕様](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="afba3-1218">For more detail on the handshake process, see the [:::no-loc(SignalR)::: Hub Protocol Specification](https://github.com/aspnet/:::no-loc(SignalR):::/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="afba3-1219">詳細設定オプションの構成</span><span class="sxs-lookup"><span data-stu-id="afba3-1219">Configure additional options</span></span>

<span data-ttu-id="afba3-1220">追加のオプションは、 `WithUrl` `withUrl` Java クライアントのの (JavaScript では) メソッド `HubConnectionBuilder` またはのさまざまな構成 api で構成でき `HttpHubConnectionBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1220">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="afba3-1221">.NET</span><span class="sxs-lookup"><span data-stu-id="afba3-1221">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="afba3-1222">.NET オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1222">.NET Option</span></span> |  <span data-ttu-id="afba3-1223">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1223">Default value</span></span> | <span data-ttu-id="afba3-1224">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1224">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="afba3-1225">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-1225">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="afba3-1226">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1226">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-1227">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1227">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-1228">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-1228">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="afba3-1229">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1229">Empty</span></span> | <span data-ttu-id="afba3-1230">認証要求に送信する TLS 証明書のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-1230">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `:::no-loc(Cookie):::s` | <span data-ttu-id="afba3-1231">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1231">Empty</span></span> | <span data-ttu-id="afba3-1232">:::no-loc(cookie):::すべての http 要求と共に送信する http のコレクション。</span><span class="sxs-lookup"><span data-stu-id="afba3-1232">A collection of HTTP :::no-loc(cookie):::s to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="afba3-1233">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1233">Empty</span></span> | <span data-ttu-id="afba3-1234">すべての HTTP 要求と共に送信する資格情報。</span><span class="sxs-lookup"><span data-stu-id="afba3-1234">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="afba3-1235">5 秒</span><span class="sxs-lookup"><span data-stu-id="afba3-1235">5 seconds</span></span> | <span data-ttu-id="afba3-1236">Websocket のみ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1236">WebSockets only.</span></span> <span data-ttu-id="afba3-1237">サーバーが終了要求を確認するのを終了した後にクライアントが待機する最大時間。</span><span class="sxs-lookup"><span data-stu-id="afba3-1237">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="afba3-1238">この時間内にサーバーが終了を認識しない場合、クライアントは切断されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1238">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="afba3-1239">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1239">Empty</span></span> | <span data-ttu-id="afba3-1240">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1240">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="afba3-1241">`HttpMessageHandler`HTTP 要求を送信するために使用されるを構成または置き換えるために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-1241">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="afba3-1242">WebSocket 接続には使用されません。</span><span class="sxs-lookup"><span data-stu-id="afba3-1242">Not used for WebSocket connections.</span></span> <span data-ttu-id="afba3-1243">このデリゲートは null 以外の値を返す必要があり、パラメーターとして既定値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1243">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="afba3-1244">既定値の設定を変更して返すか、または新しいインスタンスを返し `HttpMessageHandler` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1244">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="afba3-1245">**ハンドラーを置き換えるときに、提供されたハンドラーから保持する設定をコピーしてください。それ以外の場合、構成されているオプション ( :::no-loc(Cookie)::: s やヘッダーなど) は新しいハンドラーに適用されません。**</span><span class="sxs-lookup"><span data-stu-id="afba3-1245">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as :::no-loc(Cookie):::s and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="afba3-1246">HTTP 要求を送信するときに使用する HTTP プロキシ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1246">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="afba3-1247">このブール値を設定すると、HTTP および Websocket 要求の既定の資格情報が送信されます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1247">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="afba3-1248">これにより、Windows 認証を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1248">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="afba3-1249">追加の WebSocket オプションを構成するために使用できるデリゲート。</span><span class="sxs-lookup"><span data-stu-id="afba3-1249">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="afba3-1250">オプションの構成に使用できる [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) のインスタンスを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="afba3-1250">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="afba3-1251">JavaScript</span><span class="sxs-lookup"><span data-stu-id="afba3-1251">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="afba3-1252">JavaScript オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1252">JavaScript Option</span></span> | <span data-ttu-id="afba3-1253">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1253">Default Value</span></span> | <span data-ttu-id="afba3-1254">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1254">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="afba3-1255">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-1255">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `transport` | `null` | <span data-ttu-id="afba3-1256"><xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType>接続に使用するトランスポートを指定する値です。</span><span class="sxs-lookup"><span data-stu-id="afba3-1256">An <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType> value specifying the transport to use for the connection.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="afba3-1257">`true`クライアントによって送受信されたメッセージのバイト数または文字数をログに記録するには、をに設定します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1257">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="afba3-1258">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1258">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-1259">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1259">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-1260">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-1260">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="afba3-1261">Java</span><span class="sxs-lookup"><span data-stu-id="afba3-1261">Java</span></span>](#tab/java)

| <span data-ttu-id="afba3-1262">Java オプション</span><span class="sxs-lookup"><span data-stu-id="afba3-1262">Java Option</span></span> | <span data-ttu-id="afba3-1263">既定値</span><span class="sxs-lookup"><span data-stu-id="afba3-1263">Default Value</span></span> | <span data-ttu-id="afba3-1264">[説明]</span><span class="sxs-lookup"><span data-stu-id="afba3-1264">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="afba3-1265">HTTP 要求でベアラー認証トークンとして指定された文字列を返す関数。</span><span class="sxs-lookup"><span data-stu-id="afba3-1265">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="afba3-1266">ネゴシエーションの手順をスキップするには、これをに設定 `true` します。</span><span class="sxs-lookup"><span data-stu-id="afba3-1266">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="afba3-1267">**Websocket トランスポートが有効なトランスポートのみである場合にのみサポートされ** ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1267">**Only supported when the WebSockets transport is the only enabled transport** .</span></span> <span data-ttu-id="afba3-1268">Azure サービスを使用している場合、この設定を有効にすることはできません :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="afba3-1268">This setting can't be enabled when using the Azure :::no-loc(SignalR)::: Service.</span></span> |
| <span data-ttu-id="afba3-1269">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="afba3-1269">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="afba3-1270">Empty</span><span class="sxs-lookup"><span data-stu-id="afba3-1270">Empty</span></span> | <span data-ttu-id="afba3-1271">すべての HTTP 要求と共に送信する追加の HTTP ヘッダーのマップ。</span><span class="sxs-lookup"><span data-stu-id="afba3-1271">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="afba3-1272">.NET クライアントでは、これらのオプションは、に用意されているオプションのデリゲートによって変更でき `WithUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1272">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.:::no-loc(Cookie):::s.Add(new :::no-loc(Cookie):::(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="afba3-1273">JavaScript クライアントでは、次のように提供される JavaScript オブジェクトでこれらのオプションを指定でき `withUrl` ます。</span><span class="sxs-lookup"><span data-stu-id="afba3-1273">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="afba3-1274">Java クライアントでは、これらのオプションは、 `HttpHubConnectionBuilder` から返されるのメソッドを使用して構成できます。 `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="afba3-1274">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="afba3-1275">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="afba3-1275">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
