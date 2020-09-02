---
title: ASP.NET Core 向けの gRPC のパフォーマンスに関するベスト プラクティス
author: jamesnk
description: 高パフォーマンスの gRPC サービスを構築するためのベスト プラクティスについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876725"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="914c0-103">ASP.NET Core 向けの gRPC のパフォーマンスに関するベスト プラクティス</span><span class="sxs-lookup"><span data-stu-id="914c0-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="914c0-104">作成者: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="914c0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="914c0-105">gRPC は、高パフォーマンス サービス向けに設計されています。</span><span class="sxs-lookup"><span data-stu-id="914c0-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="914c0-106">このドキュメントでは、gRPC から最大限のパフォーマンスを得る方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="914c0-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="914c0-107">チャネルの再利用</span><span class="sxs-lookup"><span data-stu-id="914c0-107">Reuse channel</span></span>

<span data-ttu-id="914c0-108">gRPC の呼び出しを行う場合は、gRPC チャネルを再利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="914c0-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="914c0-109">チャネルを再利用すると、既存の HTTP/2 接続を介して、呼び出しを多重化できます。</span><span class="sxs-lookup"><span data-stu-id="914c0-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="914c0-110">gRPC 呼び出しごとに新しいチャネルが作成された場合、完了までにかかる時間が大幅に増加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="914c0-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="914c0-111">呼び出しのたびに HTTP/2 接続が作成されるため、クライアントとサーバーの間に複数のネットワーク ラウンドトリップが必要になります。</span><span class="sxs-lookup"><span data-stu-id="914c0-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="914c0-112">ソケットを開く</span><span class="sxs-lookup"><span data-stu-id="914c0-112">Opening a socket</span></span>
2. <span data-ttu-id="914c0-113">TCP 接続を確立する</span><span class="sxs-lookup"><span data-stu-id="914c0-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="914c0-114">TLS ネゴシエーションを行う</span><span class="sxs-lookup"><span data-stu-id="914c0-114">Negotiating TLS</span></span>
4. <span data-ttu-id="914c0-115">HTTP/2 接続を開始する</span><span class="sxs-lookup"><span data-stu-id="914c0-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="914c0-116">gRPC 呼び出しを行う</span><span class="sxs-lookup"><span data-stu-id="914c0-116">Making the gRPC call</span></span>

<span data-ttu-id="914c0-117">チャネルは、gRPC 呼び出しの間で安全に共有し、再利用できます。</span><span class="sxs-lookup"><span data-stu-id="914c0-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="914c0-118">gRPC クライアントはチャネルを使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="914c0-119">gRPC クライアントは軽量なオブジェクトであり、キャッシュしたり再利用したりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="914c0-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="914c0-120">異なる種類のクライアントを含め、1 つチャネルから複数の gRPC クライアントを作成できます。</span><span class="sxs-lookup"><span data-stu-id="914c0-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="914c0-121">チャネルおよびそのチャネルから作成されたクライアントは、複数のスレッドで安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="914c0-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="914c0-122">チャネルから作成されたクライアントは、複数の同時呼び出しを行えます。</span><span class="sxs-lookup"><span data-stu-id="914c0-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="914c0-123">接続の同時実行</span><span class="sxs-lookup"><span data-stu-id="914c0-123">Connection concurrency</span></span>

<span data-ttu-id="914c0-124">HTTP/2 接続では、通常、1 つの接続で[同時に実行できるストリームの最大数 (アクティブな HTTP 要求数)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) に制限があります。</span><span class="sxs-lookup"><span data-stu-id="914c0-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="914c0-125">既定では、ほとんどのサーバーの同時に実行できるストリームの制限数は 100 に設定されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="914c0-126">gRPC チャネルは 1 つの HTTP/2 接続を使用し、その接続で同時呼び出しが多重化されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="914c0-127">アクティブな呼び出しの数が接続ストリームの制限に達すると、追加の呼び出しがクライアントのキューに入れられます。</span><span class="sxs-lookup"><span data-stu-id="914c0-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="914c0-128">キューに入れられた呼び出しは、アクティブな呼び出しが完了するのを待ってから送信されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="914c0-129">高負荷のアプリケーションや、長時間実行されるストリーミング gRPC 呼び出しでは、この制限により、呼び出しキューが原因でパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="914c0-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="914c0-130">.NET 5 により、`SocketsHttpHandler.EnableMultipleHttp2Connections` プロパティが導入されています。</span><span class="sxs-lookup"><span data-stu-id="914c0-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="914c0-131">`true` に設定すると、同時実行ストリームの制限に達したときに、チャネルによって追加の HTTP/2 接続が作成されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="914c0-132">`GrpcChannel` が作成されると、その内部の `SocketsHttpHandler` が、追加の HTTP/2 接続を作成するように自動的に構成されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="914c0-133">アプリによって独自のハンドラーが構成される場合は、`EnableMultipleHttp2Connections` を `true` に設定することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="914c0-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="914c0-134">.NET Core 3.1 アプリには、2 つの回避策があります。</span><span class="sxs-lookup"><span data-stu-id="914c0-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="914c0-135">高負荷のアプリの領域に対して、別々の gRPC チャネルを作成します。</span><span class="sxs-lookup"><span data-stu-id="914c0-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="914c0-136">たとえば、`Logger` gRPC サービスに高い負荷がかかることがあります。</span><span class="sxs-lookup"><span data-stu-id="914c0-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="914c0-137">別個のチャネルを使用して、このアプリに `LoggerClient` を作成します。</span><span class="sxs-lookup"><span data-stu-id="914c0-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="914c0-138">gRPC チャネルのプールを使用します。たとえば、gRPC チャネルの一覧を作成します。</span><span class="sxs-lookup"><span data-stu-id="914c0-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="914c0-139">`Random` は、gRPC チャネルが必要になるたびに一覧からチャネルを選択するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="914c0-140">`Random` を使用すると、複数の接続で呼び出しがランダムに分散されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="914c0-141">この問題を解決するもう 1 つの方法は、サーバーの最大同時実行ストリーム制限を増やすことです。</span><span class="sxs-lookup"><span data-stu-id="914c0-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="914c0-142">Kestrel では、これは <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> で構成されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="914c0-143">最大同時実行ストリーム制限を増やすことはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="914c0-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="914c0-144">1 つの HTTP/2 接続でストリームが多すぎると、パフォーマンスの新たな問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="914c0-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="914c0-145">ストリーム間のスレッド競合により、接続への書き込みが試行されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="914c0-146">接続パケットが失われると、TCP 層ですべての呼び出しがブロックされます。</span><span class="sxs-lookup"><span data-stu-id="914c0-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="914c0-147">キープ アライブ ping</span><span class="sxs-lookup"><span data-stu-id="914c0-147">Keep alive pings</span></span>

<span data-ttu-id="914c0-148">キープ アライブ ping を使用して、非アクティブな状態の間 HTTP/2 接続を維持することができます。</span><span class="sxs-lookup"><span data-stu-id="914c0-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="914c0-149">アプリでアクティビティが再開されるときに既存の HTTP/2 接続が準備できていると、接続の再確立による遅延が発生することなく、初期の gRPC 呼び出しをすばやく行うことができます。</span><span class="sxs-lookup"><span data-stu-id="914c0-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="914c0-150">キープ アライブ ping は <xref:System.Net.Http.SocketsHttpHandler> に構成されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="914c0-151">上記のコードにより、非アクティブな状態の間 60 秒ごとにキープ アライブ ping をサーバーに送信するチャネルが構成されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="914c0-152">ping により、サーバーと使用中のすべてのプロキシが、非アクティブが理由で接続が閉じられることがないように保証されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="914c0-153">ストリーム</span><span class="sxs-lookup"><span data-stu-id="914c0-153">Streaming</span></span>

<span data-ttu-id="914c0-154">gRPC 双方向ストリーミングを使用して、高パフォーマンスのシナリオで単項 gRPC 呼び出しを置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="914c0-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="914c0-155">双方向ストリームが開始されると、メッセージのやり取りが、複数の単項 gRPC 呼び出しでメッセージを送信するよりも高速になります。</span><span class="sxs-lookup"><span data-stu-id="914c0-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="914c0-156">ストリーム メッセージは、既存の HTTP/2 要求にデータとして送信され、各単項呼び出しに対して新しい HTTP/2 要求を作成するオーバーヘッドがなくなります。</span><span class="sxs-lookup"><span data-stu-id="914c0-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="914c0-157">サービスの例:</span><span class="sxs-lookup"><span data-stu-id="914c0-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="914c0-158">クライアントの例:</span><span class="sxs-lookup"><span data-stu-id="914c0-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="914c0-159">パフォーマンス上の理由で単項呼び出しを双方向ストリーミングに置き換えることは高度な手法であり、多くの状況では適切ではありません。</span><span class="sxs-lookup"><span data-stu-id="914c0-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="914c0-160">ストリーミング呼び出しの使用は、次の場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="914c0-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="914c0-161">高スループットまたは短い待機時間が必要とされている。</span><span class="sxs-lookup"><span data-stu-id="914c0-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="914c0-162">gRPC と HTTP/2 が、パフォーマンスのボトルネックとして特定された。</span><span class="sxs-lookup"><span data-stu-id="914c0-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="914c0-163">クライアントのワーカーが、gRPC サービスを使用して通常のメッセージを送受信している。</span><span class="sxs-lookup"><span data-stu-id="914c0-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="914c0-164">単項ではなく、ストリーミング呼び出しを使用する場合は、さらに複雑になり、制限事項が追加されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="914c0-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="914c0-165">ストリームは、サービスまたは接続エラーによって中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="914c0-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="914c0-166">エラーが発生した場合にストリームを再開するためのロジックが必要です。</span><span class="sxs-lookup"><span data-stu-id="914c0-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="914c0-167">`RequestStream.WriteAsync` は、マルチスレッドでは安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="914c0-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="914c0-168">一度に 1 つのストリームに書き込むことができるメッセージは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="914c0-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="914c0-169">1 つのストリームで複数のスレッドからメッセージを送信するには、メッセージをマーシャリングするための <xref:System.Threading.Channels.Channel%601> のようなプロデューサーまたはコンシューマー キューが必要です。</span><span class="sxs-lookup"><span data-stu-id="914c0-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="914c0-170">gRPC ストリーミング方式は、1 種類のメッセージの受信と 1 種類のメッセージの送信に制限されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="914c0-171">たとえば、`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` により、`RequestMessage` が受信され、`ResponseMessage` が送信されます。</span><span class="sxs-lookup"><span data-stu-id="914c0-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="914c0-172">`Any` と `oneof` を使用した不明なメッセージまたは条件付きメッセージに対する Protobuf のサポートにより、この制限を回避できます。</span><span class="sxs-lookup"><span data-stu-id="914c0-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
